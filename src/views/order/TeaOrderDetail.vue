<template>
  <div class="location">
    <NProgress v-if="loadingflag"/>
    <ReturnBack :rcolor="'#fff'" :bcolor="'#CCCCCC'"></ReturnBack>

  

    <!-- 商品（fuli 只有 id + price） -->
    <div class="addressBox addressBox2">
      <div class="itemTitle">商品信息</div>
      <div class="shopInfo" v-for="(f, i) in teaFuliList" :key="i">
        <div class="shopImg">
          <img style="border-radius:5px;" class="img" :src="teaLogo" alt="">
        </div>
        <div class="nameBox">
          <div class="shopName">{{order.brandName}} </div>
         
          <div class="can"><span style="font-size: 12px;font-weight: normal;color: #666666;">{{order.shopName}}</span></div>
          <div class="all">
            <div class="price">￥{{ f.price }}</div>
            <div class="number">×1</div>
          </div>
        </div>
      </div>
      <div v-if="!teaFuliList.length" class="can" style="margin-top:10px;">暂无商品明细</div>
    </div>

    <!-- 订单明细 -->
    <div class="addressBox addressBox2">
      <div class="itemTitle">订单明细</div>
      <div class="orderInfo">
        <div class="shopPrice">
          <div class="shopPriceText">商品原价</div>
          <div class="price">￥{{ order.price || '0.00' }}</div>
        </div>
        <div class="shopPrice">
          <div class="shopPriceText">实付金额</div>
          <div class="price">￥{{ order.total_fee || '0.00' }}</div>
        </div>
        <div class="shopPrice" v-if="Number(order.refundAmount) > 0">
          <div class="shopPriceText">退款金额</div>
          <div class="price" style="color:#C93F3D;">￥{{ order.refundAmount }}</div>
        </div>
        <div class="shopPrice">
          <div class="shopPriceText">合计</div>
          <div class="price price1">￥{{ order.total_fee || '0.00' }}</div>
        </div>
      </div>
    </div>

    <!-- 订单信息 -->
    <div class="addressBox addressBox2">
      <div class="itemTitle">订单信息</div>
      <div class="orderInfo">
        <div class="shopPrice">
          <div class="shopPriceText">订单状态</div>
          <div class="price">{{ teaStatusText }}</div>
        </div>
        <div class="shopPrice">
          <div class="shopPriceText">订单号</div>
          <div class="price">{{ order.order_no }}</div>
        </div>
        <div class="shopPrice" v-if="order.third_order_no">
          <div class="shopPriceText">第三方订单号</div>
          <div class="price">{{ order.third_order_no }}</div>
        </div>
        <div class="shopPrice">
          <div class="shopPriceText">下单时间</div>
          <div class="price">{{ order.add_time ? timestampToTime(order.add_time) : '-' }}</div>
        </div>
        <div class="shopPrice" v-if="order.orderstatus == 4 && order.other && order.other.refundNo">
          <div class="shopPriceText">退款单号</div>
          <div class="price">{{ order.other.refundNo }}</div>
        </div>
        <div class="shopPrice" v-if="order.fuli">
          <div class="shopPriceText">支付方式</div>
          <div class="price">{{ payTypeText(order.fuli) }}</div>
        </div>
      </div>
    </div>

    <!-- 客服 -->
    <div class="service" @click="toKefu">
      <div>如有问题，请联系客服 全年无休(9:00-21:00)</div>
      <div class="serviceBox">
        <img class="img" src="../../assets/kf.png" alt="">
      </div>
    </div>
  </div>
</template>

<script>
import {DangaoIrder} from "@/api/mine";

export default {
  name: "TeaOrderDetail",
  data() {
    return {
      order: {},
      loadingflag: true
    }
  },
  computed: {
    teaLogo() {
      return (this.order.brandInfo && this.order.brandInfo.logoThumbUrl)
        || 'http://yxfmm.bjyxfl.com/imgs/default-image.png'
    },
    teaFuliList() {
      const arr = this.safeParse(this.order.fuli, [])
      return Array.isArray(arr) ? arr : []
    },
    teaStatusText() {
      const map = { '0': '待付款', '2': '支付成功', '4': '部分退款', '5': '已取消（全部退款）' }
      return map[String(this.order.orderstatus)] || '未知状态'
    }
  },
  methods: {
    toKefu() {
      window.location.href = localStorage.getItem("kefu")
    },
    safeParse(val, fallback) {
      try {
        if (val === undefined || val === null || val === '') return fallback
        if (typeof val === 'object') return val
        return JSON.parse(val)
      } catch (e) { return fallback }
    },
    payTypeText(fuli) {
      const arr = this.safeParse(fuli, [])
      if (!Array.isArray(arr) || !arr.length) return '-'
      const wx = arr.filter(i => i && i.id == 'wx').length
      if (wx === arr.length) return '微信'
      if (wx > 0) return '微信和福利卡'
      return '福利卡'
    },
    timestampToTime(time) {
      var date = new Date(time * 1000)
      let y = date.getFullYear()
      let MM = date.getMonth() + 1
      MM = MM < 10 ? ('0' + MM) : MM
      let d = date.getDate()
      d = d < 10 ? ('0' + d) : d
      let h = date.getHours()
      h = h < 10 ? ('0' + h) : h
      let m = date.getMinutes()
      m = m < 10 ? ('0' + m) : m
      let s = date.getSeconds()
      s = s < 10 ? ('0' + s) : s
      return y + '-' + MM + '-' + d + " " + h + ":" + m + ":" + s
    },
    getDetail(id) {
      DangaoIrder({ id }).then(res => {
        this.loadingflag = false
        if (res.code == 200) {
          // 爷爷不泡茶：data.order 就是订单主对象，没有 detail/orderinfo
          this.order = res.data.order || {}
        }
      })
    }
  },
  created() {
    this.getDetail(this.$route.query.id)
  }
}
</script>

<style scoped lang="less">
.location {
  background-color: #F0F0F0;
  padding: 10px;
  min-height: 100vh;
  box-sizing: border-box;
}
.addressBox {
  padding: 13px 10px;
  background-color: white;
  border-radius: 10px;
  .address { display: flex; align-items: center; justify-content: space-between; }
  .addressIcon { width:60px; }
  .addressDetail { font-size: 13px; font-weight: bold; margin-top: 3px; }
  .userName { font-size: 15px; font-weight: bold; }
  .leftBox { display: flex; align-items: center; gap: 5px; }
}
.addressBox2 { margin-top: 10px; padding: 13px 15px 15px; position: relative; }
.itemTitle { font-weight: bold; font-size: 15px; }
.orderInfo {
  .shopPrice { display: flex; align-items: center; justify-content: space-between; margin-top: 12px; }
  .shopPriceText { color: #828282; font-size: 14px; }
  .price { font-size: 14px; font-weight: bold; }
  .price1 { color: #C93F3D; }
}
.shopInfo {
  margin-top: 15px;
  display: flex;
  align-items: flex-start;
  gap: 10px;
  .shopImg { width: 77px; height: 77px; }
  .nameBox { width: 73%; }
  .shopName { font-size: 14px; font-weight: bold; display: -webkit-box; -webkit-box-orient: vertical; overflow: hidden; -webkit-line-clamp: 2; }
  .can { margin-top: 7px; font-size: 13px; color: #979797; }
  .all { font-size: 13px; display: flex; align-items: center; justify-content: space-between; margin-top: 15px; }
  .price { color: #C63532; font-weight: bold; font-size: 15px; }
  .number { color: #989898; }
}
.service {
  background-color: white;
  color: #5b5a5a;
  font-size: 13px;
  border-radius: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 15px;
  margin-top: 10px;
  .serviceBox { width: 18px; padding-top: 1px; }
}
</style>