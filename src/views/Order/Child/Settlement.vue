<template>
  <div class="settlement">
    <app-Header :isLeft="true" title="确认订单" />
    <div class="view-body" v-if="orderInfo">
      <!-- 收货地址 -->
      <section class="cart-address">
        <p class="title">
          订单配送至
          <span v-if="userInfo&&userInfo.tag" class="address-tag">{{userInfo.tag}}</span>
        </p>
        <p class="address-detail">
          <span
            v-if="userInfo"
            @click="$router.push('/myaddress')"
          >{{userInfo.address}}{{userInfo.bottom}}</span>
          <span v-else>
            <span v-if="haveAddress" @click="$router.push('/myaddress')">选择收货地址</span>
            <span v-else>新增收货地址</span>
          </span>
          <i class="fa fa-angle-right"></i>
        </p>
        <h2 v-if="userInfo" class="address-name">
          <span>{{userInfo.name}}</span>

          <span class="phone">{{userInfo.phone}}</span>
        </h2>
      </section>
      <!-- 送达时间 -->
      <app-Delivery :shopInfo="orderInfo.shopInfo" />
      <!-- 购物车信息 -->
      <app-CartInfo :orderInfo="orderInfo" :total="total" />
    </div>
    <!-- 底部结算 -->
    <div class="total-bar">
      <span>￥{{total}}</span>
      <button @click="pay">去支付</button>
    </div>
  </div>
</template>

<script>
// 头部
import Header from "components/common/Header/Header";
//到达
import Delivery from "./Delivery";
// 购物车信息
import CartInfo from "./CartInfo";
// vuex--getters
import { mapGetters } from "vuex";
//提示
import { Toast } from "mint-ui";
export default {
  name: "Settlement",
  beforeRouteEnter(to, from, next) {
    next((vm) => {
      if (!vm.userInfo) {
        vm.getData();
      }
    });
  },
  data() {
    return {
      haveAddress: true,
    };
  },
  methods: {
    getData() {
      this.$axios(`/api/user/user_info/${localStorage.ele_login}`).then(
        (res) => {
          console.log(res.data);
          if (res.data.myAddress.length > 0) {
            this.haveAddress = true;
          } else {
            this.haveAddress = false;
          }
        }
      );
    },
    pay() {
      if (!this.userInfo) {
        Toast({
          message: "请选择收货地址",
          position: "middle",
          duration: 2000,
        });
        return;
      } else {
        this.$router.push("/pay");
      }
    },
  },
  computed: {
    ...mapGetters(["userInfo", "orderInfo", "total"]),
  },
  components: {
    "app-Header": Header,
    "app-Delivery": Delivery,
    "app-CartInfo": CartInfo,
  },
};
</script>

<style scoped>
.settlement {
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  padding-top: 44px;
}

.view-body {
  width: 100%;
  height: 100%;
  overflow: auto;
  box-sizing: border-box;
  font-size: 0.9rem;
  color: #333;
  padding-bottom: 14.133333vw;
  padding-left: 1.6vw;
  padding-right: 1.6vw;
  background-image: linear-gradient(
      0deg,
      #f5f5f5,
      #f5f5f5 65%,
      hsla(0, 0%, 96%, 0.3) 75%,
      hsla(0, 0%, 96%, 0)
    ),
    linear-gradient(270deg, #38c27e, #38c27e);
  display: flex;
  flex-direction: column;
  flex-grow: 1;
}
.cart-address {
  background-color: transparent;
  padding: 4.266667vw 2.133333vw 2.933333vw 2.133333vw;
  min-height: 22.133333vw;
  color: #fff;
  overflow: hidden;
}
.cart-address .title {
  font-size: 0.9rem;
  line-height: 1.21;
  color: hsla(0, 0%, 100%, 0.8);
}
.cart-address .address-detail {
  font-size: 1.3rem;
  font-weight: 600;
  line-height: 1.88;
  overflow: hidden;
  display: flex;
  align-items: center;
}
.address-detail > span {
  display: inline-block;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  max-width: calc(100% - 4vw);
}
.address-detail > i {
  margin-left: 2.133333vw;
}
/* 姓名电话 */
.address-name {
  font-size: 0.86rem;
  line-height: 1.21;
  margin-bottom: 1.333333vw;
}
.address-name .phone {
  margin-left: 2.133333vw;
}
.address-tag {
  border: 0.133334vw solid hsla(0, 0%, 100%, 0.8);
  margin-left: 1.6vw;
  display: inline-block;
  padding: 0.533333vw;
  white-space: nowrap;
  border-radius: 0.133333vw;
  font-size: 0.6rem !important;
  line-height: 2.666667vw;
}
/* 底部支付 */
.total-bar {
  height: 11.733333vw;
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  background: #3c3c3c;
  z-index: 2;
}
.total-bar > span {
  color: #fff;
  font-size: 1.2rem;
  line-height: 11.733333vw;
  padding-left: 2.666667vw;
  vertical-align: middle;
}
.total-bar > button {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  background: #00e067;
  min-width: 28vw;
  padding: 0 1.333333vw;
  border: none;
  color: #fff;
  font-size: 1.2rem;
  outline: none;
}
</style>