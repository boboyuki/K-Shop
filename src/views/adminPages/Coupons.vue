 <template>
  <div class="text-center">
    <loading :active.sync="isLoading">
      <template name="default">
        <div class="bazzi-loading"></div>
      </template>
    </loading>
    <div class="text-right mt-4">
      <button class="btn btn-primary" @click=" openModal('set')">建立優惠券</button>
    </div>
    <table class="table mt-4">
      <thead>
        <tr>
          <th>優惠名稱</th>
          <th>啟用</th>
          <th>折扣</th>
          <th>截止日期</th>
          <th>優惠碼</th>
          <th>編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item) in coupons" :key="item.id">
          <td>{{item.title}}</td>
          <td>
            <span class="text-success" v-if="item._is_enable">啟用</span>
            <span class="text-success" v-else>未啟用</span>
          </td>
          <td>{{item.percent}} %</td>
          <td>{{item.due_date | date}}</td>
          <td>{{item.code}}</td>
          <td>
            <button class="btn btn-outline-primary btn-sm mr-1" @click=" openModal('edit',item)">編輯</button>
            <button class="btn btn-outline-danger btn-sm" @click="openModal('delete',item)">刪除</button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- pagination -->
    <Pagination :pagination="pagination" @getpage="getCoupons" v-if="pagination"></Pagination>
    <!-- Modal-edit,set -->
    <div
      class="modal fade"
      id="couponModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="couponModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-sm" role="document">
        <div class="modal-content border-0">
          <div class="modal-header bg-dark text-white">
            <h5 class="modal-title" id="couponModalLabel">
              <span v-if="isNew === true">新增優惠券</span>
              <span v-else>編輯優惠券</span>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span class="text-white" aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="row">
              <div class="col">
                <div class="form-group">
                  <label for="couponTitle">優惠券名稱</label>
                  <input
                    type="text"
                    class="form-control"
                    id="couponTitle"
                    placeholder="請輸入優惠券"
                    v-model="tempCoupon.title"
                  />
                </div>

                <div class="form-group">
                  <label for="discount">折扣</label>
                  <input
                    type="number"
                    class="form-control"
                    id="discount"
                    placeholder="請輸入折扣%數"
                    v-model="tempCoupon.percent"
                  />
                </div>
                <div class="form-group">
                  <label for="due_date">截止日期</label>
                  <input type="date" class="form-control" id="due_date" v-model="due_date" />
                </div>
                <div class="form-group">
                  <label for="code">優惠碼</label>
                  <input
                    type="text"
                    class="form-control"
                    id="code"
                    placeholder="請輸入優惠碼"
                    v-model="tempCoupon.code"
                  />
                </div>
                <hr />
                <div class="form-check">
                  <input
                    type="checkbox"
                    class="form-check-input"
                    id="is_enable"
                    placeholder="是否啟用"
                    v-model="tempCoupon._is_enable"
                  />
                  <label
                    for="is_enable"
                    class="form-check-label text-success"
                    v-if="tempCoupon._is_enable"
                  >已啟用</label>
                  <label for="is_enable" class="form-check-label" v-else>未啟用</label>
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
            <button type="button" class="btn btn-primary" @click="updateCoupon">確認</button>
          </div>
        </div>
      </div>
    </div>
    <!-- Moadal-delete -->
    <div
      class="modal fade"
      id="delCouponModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content border-0">
          <div class="modal-header bg-danger text-white">
            <h5 class="modal-title" id="exampleModalLabel">
              <span>刪除優惠券</span>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            是否刪除
            <strong class="text-danger">{{ tempCoupon.title }}</strong> 優惠券(刪除後將無法恢復)。
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
            <button type="button" class="btn btn-danger" @click="updateCoupon">確認刪除</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import $ from 'jquery'
import Cookies from 'js-cookie'
import Pagination from '@/components/common/Pagination'
export default {
  data () {
    return {
      coupons: [],
      tempCoupon: {
      },
      due_date: new Date(),
      isNew: false,
      openModalMethod: '',
      isLoading: false,
      pagination: {}

    }
  },
  components: {
    Pagination
  },
  methods: {
    getCoupons (page = 1) {
      const vm = this
      const api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/coupons?page=${page}`
      vm.isLoading = true
      this.$http.get(api).then(res => {
        vm.coupons = res.data.coupons
        vm.pagination = res.data.pagination
        console.log(res.data.coupons.due_date)
        vm.isLoading = false
      })
    },
    openModal (openModalMethod, item) {
      if (openModalMethod === 'set') {
        this.tempCoupon = {}
        this.isNew = true
        this.openModalMethod = 'set'
        $('#couponModal').modal('show')
      } else if (openModalMethod === 'edit') {
        this.tempCoupon = Object.assign({}, item)
        this.isNew = false
        this.openModalMethod = 'edit'
        $('#couponModal').modal('show')
      } else if (openModalMethod === 'delete') {
        this.tempCoupon = item
        this.isNew = false
        this.openModalMethod = 'delete'
        $('#delCouponModal').modal('show')
      }
    },
    updateCoupon () {
      const vm = this
      console.log(Cookies.get('token'))
      // 預設為post
      let api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/coupon`
      let httpMethod = 'post'
      if (vm.openModalMethod === 'edit') {
        api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/coupon/${vm.tempCoupon.id}`
        httpMethod = 'put'
      } else if (vm.openModalMethod === 'delete') {
        api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/coupon/${vm.tempCoupon.id}`
        httpMethod = 'delete'
      }
      this.$http[httpMethod](api, { data: vm.tempCoupon }, {
        headers: {
          Authorization: `${Cookies.get('token')}`
        }
      }
      ).then(res => {
        const modal = vm.openModalMethod === 'delete' ? '#delCouponModal' : '#couponModal'
        if (res.data.success) {
          $(modal).modal('hide')
          vm.getCoupons()
        } else {
          $(modal).modal('hide')
          vm.getCoupons()
        }
      })
    }
  },
  created () {
    this.getCoupons()
  },
  watch: {
    due_date () {
      const vm = this
      const timestamp = Math.floor(new Date(vm.due_date) / 1000)
      vm.tempCoupon.due_date = timestamp
    }
  }

}
</script>
