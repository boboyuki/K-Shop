
<template>
  <div class="text-center">
    <loading :active.sync="isLoading">
      <template name="default">
        <div class="bazzi-loading"></div>
      </template>
    </loading>
    <table class="table mt-4">
      <thead>
        <tr>
          <th>名稱</th>
          <th>Email</th>
          <th>電話</th>
          <th>訂單內容</th>
          <th>付款金額</th>
          <th>是否付款</th>
          <th>修改訂單</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(item) in sortOrders"
          :key="item.id"
          class="text-overflow font-weight-bold"
          :class="{'text-muted':!item.is_paid}"
        >
          <td>{{item.user.name}}</td>
          <td>{{item.user.email}}</td>
          <td>{{item.user.tel}}</td>
          <td width="300" class="text-left pl-5">
            <p
              class="m-0"
              v-for="(product) in item.products"
              :key="product.id"
            >{{product.product.title}} : {{product.qty}} {{product.product.unit}}</p>
          </td>
          <td>{{item.total | currency}}</td>
          <td>
            <span v-if="item.is_paid" class="text-success">付款成功</span>
            <span v-else>尚未付款</span>
          </td>
          <td>
            <button class="btn btn-outline-primary btn-sm mr-1" @click="openModal(item)">修改</button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- pagination -->
    <Pagination :pagination="pagination" @getpage="getOrders"></Pagination>
    <!-- Modal -->
    <div
      class="modal fade"
      id="orderModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="productsModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content border-0">
          <div class="modal-header bg-dark text-white">
            <h5 class="modal-title" id="productsModalLabel">
              <span>編輯訂單</span>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span class="text-white" aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="row">
              <div class="col-6 text-left">
                <h3>訂購人資料</h3>
                <div class="form-group">
                  <label for="username">名稱</label>
                  <input
                    type="text"
                    class="form-control"
                    id="username"
                    placeholder="更改名稱"
                    v-model="tempOrder.user.name"
                  />
                </div>
                <div class="form-group">
                  <label for="email">email</label>
                  <input
                    type="text"
                    class="form-control"
                    id="email"
                    placeholder="更改email"
                    v-model="tempOrder.user.email"
                  />
                </div>
                <div class="form-group">
                  <label for="tel">電話</label>
                  <input
                    type="text"
                    class="form-control"
                    id="tel"
                    placeholder="更改電話"
                    v-model="tempOrder.user.tel"
                  />
                </div>
                <div class="form-group">
                  <label for="address">地址</label>
                  <input
                    type="text"
                    class="form-control"
                    id="address"
                    placeholder="更改地址"
                    v-model="tempOrder.user.address"
                  />
                </div>
              </div>
              <div class="col-6 text-left">
                <h3>產品資訊</h3>
                <div class="pre-scrollable">
                  <div class="form-group" v-for="item in tempOrder.products" :key="item.id">
                    <label for="product_qty">
                      商品名稱 :
                      <span class="text-primary">{{item.product.title}}</span>
                    </label>
                    <input
                      type="number"
                      class="form-control"
                      id="product_qty"
                      placeholder="請輸入數量"
                      v-model="item.qty"
                    />
                  </div>
                </div>

                <div class="form-check text-center mt-5">
                  <input
                    class="form-check-input"
                    type="checkbox"
                    id="is_paid"
                    v-model="tempOrder.is_paid"
                  />
                  <label
                    for="is_paid"
                    class="text-success form-check-label"
                    v-if="tempOrder.is_paid"
                  >已付款</label>
                  <label for="is_paid" class="form-check-label" v-else>未付款</label>
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
            <button type="button" class="btn btn-primary" @click="updateOrder(tempOrder.id)">確認</button>
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
      orders: [],
      tempOrder: {
        products: [],
        user: {}
      },
      isLoading: false,
      pagination: {}
    }
  },
  components: {
    Pagination: Pagination
  },
  methods: {
    getOrders (page = 1) {
      const vm = this
      const api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/orders?page=${page}`
      vm.isLoading = true
      this.$http.get(api, {
        headers: {
          Authorization: `${Cookies.get('token')}`
        }
      }).then(res => {
        vm.orders = res.data.orders
        vm.isLoading = false
        vm.pagination = res.data.pagination
      })
    },
    openModal (item) {
      this.tempOrder = Object.assign({}, item)
      $('#orderModal').modal('show')
    },
    updateOrder (orderId) {
      const vm = this
      const api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/order/${orderId}`
      this.$http.put(api, { data: vm.tempOrder }, {
        headers: {
          Authorization: `${Cookies.get('token')}`
        }
      }).then(res => {
        if (res.data.success) {
          $('#orderModal').modal('hide')
          vm.getOrders()
        } else {
          $('#orderModal').modal('hide')
          vm.getOrders()
          alert('修改失敗')
        }
      })
    }
  },
  computed: {
    sortOrders () {
      const vm = this
      let OrderList = []
      if (vm.orders.length) {
        OrderList = vm.orders.sort((a, b) => {
          const aPaid = a.is_paid
          const bPaid = b.is_paid
          return bPaid - aPaid
        })
      }
      return OrderList
    }
  },
  created () {
    this.getOrders()
  }
}

</script>
<style scoped>
.text-overflow {
  width: 200px;
  height: 100px;
  overflow: hidden;
  text-overflow:ellipsis;
}
</style>
