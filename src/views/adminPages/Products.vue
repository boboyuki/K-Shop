 <template>
  <div class="text-center">
    <loading :active.sync="isLoading">
      <template name="default">
        <div class="bazzi-loading"></div>
      </template>
    </loading>
    <div class="text-right mt-4">
      <button class="btn btn-primary" @click=" openModal('set')">建立新產品</button>
    </div>
    <table class="table mt-4">
      <thead>
        <tr>
          <th>分類</th>
          <th>產品名稱</th>
          <th>原價</th>
          <th>售價</th>
          <th>啟用</th>
          <th>編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item) in products" :key="item.id">
          <td>{{item.category}}</td>
          <td>{{item.title}}</td>
          <td class="text-right">{{item.origin_price | currency}}</td>
          <td class="text-right">{{item.price | currency}}</td>
          <td>
            <span class="text-success" v-if="item.is_enabled">啟用</span>
            <span class="text-success" v-else>未啟用</span>
          </td>
          <td>
            <button class="btn btn-outline-primary btn-sm mr-1" @click=" openModal('edit',item)">編輯</button>
            <button class="btn btn-outline-danger btn-sm" @click="openModal('delete',item)">刪除</button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- pagination -->
    <Pagination :pagination="pagination" @getpage="getProducts"></Pagination>
    <!-- Modal-edit,set -->
    <div
      class="modal fade"
      id="productsModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="productsModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content border-0">
          <div class="modal-header bg-dark text-white">
            <h5 class="modal-title" id="productsModalLabel">
              <span v-if="isNew === true">新增產品</span>
              <span v-else>編輯產品</span>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span class="text-white" aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="row">
              <div class="col-sm-4">
                <div class="form-group">
                  <label for="image">輸入圖片網址</label>
                  <input
                    type="text"
                    class="form-control"
                    id="image"
                    placeholder="請輸入圖片連結"
                    v-model="tempProduct.imageUrl"
                  />
                </div>
                <div class="form-group">
                  <label for="customFile">
                    或 上傳圖片
                    <i class="fas fa-cog fa-spin" v-if="fileUploading"></i>
                  </label>
                  <input
                    type="file"
                    id="customFile"
                    class="form-control py-1"
                    ref="files"
                    @change="updateFile"
                  />
                </div>
                <img
                  img="https://images.unsplash.com/photo-1483985988355-763728e1935b?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=828346ed697837ce808cae68d3ddc3cf&auto=format&fit=crop&w=1350&q=80"
                  class="img-fluid"
                  :src="tempProduct.imageUrl"
                  alt
                />
              </div>
              <div class="col-sm-8">
                <div class="form-group">
                  <label for="title">標題</label>
                  <input
                    type="text"
                    class="form-control"
                    id="title"
                    placeholder="請輸入標題"
                    v-model="tempProduct.title"
                  />
                </div>

                <div class="form-row">
                  <div class="form-group col-md-6">
                    <label for="category">分類</label>
                    <input
                      type="text"
                      class="form-control"
                      id="category"
                      placeholder="請輸入分類"
                      v-model="tempProduct.category"
                    />
                  </div>
                  <div class="form-group col-md-6">
                    <label for="price">單位</label>
                    <input
                      type="unit"
                      class="form-control"
                      id="unit"
                      placeholder="請輸入單位"
                      v-model="tempProduct.unit"
                    />
                  </div>
                </div>

                <div class="form-row">
                  <div class="form-group col-md-6">
                    <label for="origin_price">原價</label>
                    <input
                      type="number"
                      class="form-control"
                      id="origin_price"
                      placeholder="請輸入原價"
                      v-model="tempProduct.origin_price"
                      required
                    />
                  </div>
                  <div class="form-group col-md-6">
                    <label for="price">售價</label>
                    <input
                      type="number"
                      class="form-control"
                      id="price"
                      placeholder="請輸入售價"
                      v-model="tempProduct.price"
                    />
                  </div>
                </div>
                <hr />

                <div class="form-group">
                  <label for="description">產品描述</label>
                  <textarea
                    type="text"
                    wrap="hard"
                    style="white-space: pre-line;"
                    class="form-control"
                    id="description"
                    placeholder="請輸入產品描述"
                    v-model="tempProduct.description"
                  ></textarea>
                </div>
                <div class="form-group">
                  <label for="content">說明內容</label>
                  <textarea
                    type="text"
                    wrap="hard"
                    style="white-space: pre-line;"
                    class="form-control"
                    id="content"
                    placeholder="請輸入產品說明內容"
                    v-model="tempProduct.content"
                  ></textarea>
                </div>
                <div class="form-group">
                  <div class="form-check">
                    <input
                      class="form-check-input"
                      type="checkbox"
                      id="is_enabled"
                      v-model="tempProduct.is_enabled"
                      true-value="1"
                      false-value="0"
                    />
                    <label class="form-check-label" for="is_enabled">是否啟用</label>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
            <button type="button" class="btn btn-primary" @click="updateProduct">確認</button>
          </div>
        </div>
      </div>
    </div>
    <!-- Moadal-delete -->
    <div
      class="modal fade"
      id="delProductModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content border-0">
          <div class="modal-header bg-danger text-white">
            <h5 class="modal-title" id="exampleModalLabel">
              <span>刪除產品</span>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            是否刪除
            <strong class="text-danger">{{ tempProduct.title }}</strong> 商品(刪除後將無法恢復)。
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
            <button type="button" class="btn btn-danger" @click="updateProduct">確認刪除</button>
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
      products: [],
      tempProduct: {
        price: 0,
        origin_price: 0
      },
      isNew: false,
      openModalMethod: '',
      isLoading: false,
      fileUploading: false,
      pagination: {}
    }
  },
  components: {
    Pagination
  },
  methods: {
    getProducts (page = 1) {
      const vm = this
      const api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/products?page=${page}`
      vm.isLoading = true
      this.$http.get(api, {
        headers: {
          Authorization: `${Cookies.get('token')}`
        }
      }).then(res => {
        console.log(res)
        vm.products = res.data.products
        vm.isLoading = false
        vm.pagination = res.data.pagination
      })
    },
    openModal (openModalMethod, item) {
      if (openModalMethod === 'set') {
        this.tempProduct = {}
        this.isNew = true
        this.openModalMethod = 'set'
        $('#productsModal').modal('show')
      } else if (openModalMethod === 'edit') {
        this.tempProduct = Object.assign({}, item)
        this.isNew = false
        this.openModalMethod = 'edit'
        $('#productsModal').modal('show')
      } else if (openModalMethod === 'delete') {
        this.tempProduct = item
        this.isNew = false
        this.openModalMethod = 'delete'
        $('#delProductModal').modal('show')
      }
    },
    updateProduct () {
      const vm = this
      // 預設為post
      let api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/product`
      let httpMethod = 'post'
      if (vm.openModalMethod === 'edit') {
        api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/product/${vm.tempProduct.id}`
        httpMethod = 'put'
      } else if (vm.openModalMethod === 'delete') {
        api = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/product/${vm.tempProduct.id}`
        httpMethod = 'delete'
      }
      this.$http[httpMethod](api, { data: vm.tempProduct }, {
        headers: {
          Authorization: `${Cookies.get('token')}`
        }
      }).then(res => {
        const modal = vm.openModalMethod === 'delete' ? '#delProductModal' : '#productsModal'
        if (res.data.success) {
          $(modal).modal('hide')
          this.$bus.$emit('message:push', res.data.message, 'success')
          vm.getProducts()
        } else {
          $(modal).modal('hide')
          this.$bus.$emit('message:push', res.data.message, 'danger')
          vm.getProducts()
        }
      })
    },
    updateFile () {
      const uploadedFile = this.$refs.files.files[0]
      const vm = this
      const formData = new FormData()
      vm.fileUploading = true
      formData.append('file-to-upload', uploadedFile)
      const url = `${process.env.VUE_APP_APIPATH}/api/${process.env.VUE_APP_CUSTOMPATH}/admin/upload/`
      this.$http.post(url, formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
          Authorization: `${Cookies.get('token')}`
        }
      }).then(res => {
        vm.fileUploading = false
        if (res.data.success) {
          vm.$set(vm.tempProduct, 'imageUrl', res.data.imageUrl)
        } else {
          this.$bus.$emit('message:push', res.data.message, 'danger')
        }
      })
    }
  },
  created () {
    this.getProducts()
  }
}
</script>
