<template>
  <div class="swiper">
    <swiper ref="mySwiper" :options="swiperOptions">
      <swiper-slide v-for="item in productsList" :key="item.id">
        <div class="swiper-picture">
          <img class="swiper-img" :src="item.imageUrl" alt="item.title" />
          <div class="swiper-info">
            <div
              class="swiper-info--btn btn btn-outline-primary"
              @click.prevent="directProduct(item.id)"
            >前往商品</div>
          </div>
        </div>
      </swiper-slide>
    </swiper>
  </div>
</template>

<script>
export default {
  name: 'carrousel',
  props: ['productsList'],
  data () {
    return {
      swiperOptionMuch: {
        slidesPerView: 1,
        spaceBetween: 20,
        speed: 500,
        autoplay: true,
        breakpoints: {
          768: {
            slidesPerView: 2,
            spaceBetween: 30
          },
          992: {
            slidesPerView: 3,
            spaceBetween: 40
          },
          1280: {
            slidesPerView: 4,
            spaceBetween: 50
          }
        },
        pagination: {
          el: '.swiper-pagination',
          clickable: true
        }
      }
    }
  },
  computed: {
    swiper () {
      return this.$refs.mySwiper.$swiper
    }
  },
  methods: {
    directProduct (id) {
      this.$emit('directProduct', id)
    }
  }
}
</script>
<style lang="scss" scoped>
  .swiper {
    &-picture {
      width: 90%;
      height: 100%;
      position: relative;
      background-color: #343a40;

      &:last-child {
        margin-right: 0;
      }
    }
    &-img {
      width: 100%;
      height: 100%;
      position: relative;
      background-color: #343a40;
    }
    &-info {
      width: 100%;
      height: 0;
      background: rgba(245, 245, 245, 0.45);
      position: absolute;
      top: 0;
      left: 0;
      transition: display 0.5s;
      &--btn {
        position: absolute;
        opacity: 0;
        width: 80%;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
      }
    }
    &-button-next {
      right: 0;
    }
    &-picture:hover {
      .swiper-info {
        height: 100%;
      }
      .swiper-info--btn {
        opacity: 1;
      }
    }
  }
  .my-button-disabled {
    opacity: 0;
  }
</style>
