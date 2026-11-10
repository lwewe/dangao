<template>
  <div class="location">
    <NProgress v-if="loadingflag" />
    <div class="modelNo" v-if="isCityId">
      <!--      <div class="bad">定位失败，请选择城市</div>-->
    </div>
    <!--    头部-->
    <div class="topBox" ref="topBox">
      <div class="topPage">
        <!--      <div class="title">悦享聚汇</div>-->
        <div class="searchBox">
          <div>
            <!--      地址-->
            <City :type="1" @getCityId="getCityId"></City>
          </div>
          <div class="searchValue" @click="toOtherNext('/birthdayCake?search=1')" v-if="!isCityId">
            <van-search background="#fff0" placeholder="搜索你想要的商品" v-model="searchValue" />
          </div>
          <div class="customer" @click="tokf">
            <img class="img" src="../assets/kf1.png" alt="">
          </div>
        </div>
      </div>
      <!--  banner-->
      <!--  banner@change="onChange"-->
      <div class="banner" v-if="banneerList.length > 0">
        <van-swipe :autoplay="3000" indicator-color="white">
          <van-swipe-item class="bannerImg" @click="toOtherdetail(item)" v-for="item in banneerList" :key="item.id">
            <img class="img" :src="item.img" alt="">
          </van-swipe-item>
        </van-swipe>
      </div>
    </div>


    <!-- add -->

    <div style="padding:10px 10px 2px 10px;display: none; ">
      <div style="background-color: rgba(254, 244, 243, 1);border-radius: 10px; font-size: 12px;text-align: center;">
        <div
          style="display: flex;justify-content: space-around;padding-top:12px  ;color: rgba(36, 0, 3, 1); padding-bottom: 10px;">

          <div v-for="(item3, index) in fenl.slice(0, 4)" :key="item3.id"
            @click="brandaelectiondetailsadd(index + 1, item3.id)">
            <div> <img :src="item3.img" alt="" style="width:50px;height:50px"></div>
            <div style="font-weight: 600;">{{ item3.title }}</div>
          </div>



        </div>
        <div style="display: flex;justify-content: space-around;padding-bottom: 10px  ;">


          <div v-for="(item5, index) in fenl.slice(4, 9)" :key="item5.id"
            @click="brandaelectiondetailsadd(index + 5, item5.id)">
            <div> <img :src="item5.img" alt="" style="width:50px;height:50px"></div>
            <div style="font-weight: 600;">{{ item5.title }}</div>
          </div>


        </div>



      </div>
    </div>
    <!--  add-->
    <!--    center-->
    <div class="centerBox" ref="centerBox">
      <div class="flexBox" v-if="funList.length > 0">
        <div class="toHome" v-for="(item, index) in funList.slice(0, 1)" :key="item.id" @click="toNext(item, index)">
          <img class="img imgTitle" :src="item.img" alt="">
          <div class="titleBox">
            <div class="title">{{ item.title }}</div>
            <div class="text">{{ item.xtitle }}</div>
          </div>
        </div>
        <div class="toHome" style="display: flex;flex-direction: column;gap: 5px">
          <div style="position: relative" v-for="(item, index) in funList.slice(1, 3)" :key="item.id"
            @click="toNext(item, index)">
            <img class="img imgTitle" :src="item.img" alt="">
            <div class="titleBox">
              <div class="title">{{ item.title }}</div>
              <div class="text">{{ item.xtitle }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="birthdayBox" v-if="xfunList.length > 0">
        <!--        <div class="birthday" :style="'width:'+xfunList.length*145+'px'">-->
        <div class="birthday">

          <div class="listItem" v-if="flag != 0" style="position: relative"
            v-for="(item1, index) in xfunList.filter(item1 => item1.id != 17)" :key="item1.id"
            :style="'color:' + item1.colorText + ';'" @click="toNext(item1, index)">
            <img class="img imgTitle" :src="item1.img" alt="">
          </div>
          <div class="listItem" v-if="flag == 0" style="position: relative" v-for="(item, index) in xfunList"
            :key="item.id" :style="'color:' + item.colorText + ';'" @click="toNext(item, index)">
            <img class="img imgTitle" :src="item.img" alt="">
          </div>
        </div>

      </div>
      <!--    蛋糕品牌-->
      <div class="cakeBrand">
        <div class="brandtitleBox">
          <div class="title"><span>品牌</span><span style="color: #342C2C;margin-left: 5px;font-size: 11px;">|</span>
            <span class="bgadd" @click="addgo(1)">饮品直冲</span><span class="bgadds" @click="addgo(2)">电子券</span>
          </div>
          <div class="seeAll" @click="toOtherNext('/orderfood?active=2')">

            <div
              style="background-color: #F99E9B; border-radius: 50%;text-align: center;line-height: 22px; width: 18px;height:18px;color: #ffffff;">
              <van-icon name="arrow" />
            </div>
          </div>
        </div>
        <!-- <div class="listShop" >
          <van-swipe indicator-color="#BA6466" @change="onChangeShop" :show-indicators=false>
            <van-swipe-item v-for="(item, index) in Math.ceil(drinkBrandList.length / 5)" :key="item">
              <div class="cakeBoxImg">
                <div class="imgMax2" v-for="item2 in drinkBrandList.slice(index * 5, (index + 1) * 5)" :key="item2.id"
                  @click="toBrandDetail(item2.id, item2.brand_id)">
                  <div class="imgMax">
                    <img class="img" :src="item2.tbimg" alt="">
                  </div>
                   
                </div>
              </div>
            </van-swipe-item>
          </van-swipe>
        </div> -->

        <div class="listShop">
          <div class="cakeBoxImg">
            <div class="imgMax2" v-for="item2 in drinkBrandList.slice(0, 5)" :key="item2.id"
              @click="toBrandDetail(item2.id, item2.brand_id)">
              <div class="imgMax">
                <img class="img" :src="item2.tbimg" alt="">
              </div>
            </div>
          </div>
        </div>

      </div>

      <!--  -->

      <div v-if="gonggao" style="margin-top: 5px" @click="toNexts('/notification')">
        <van-notice-bar :text="gonggao" left-icon="volume-o" />
      </div>
      <!--  -->
    </div>
    <!-- add 随心兑 -->
    <div v-if="sxfcardfag === 1">
      <div class="exchange-section">
        <div class="exchange-card">

          <div class="exchange-header">
            <div class="header-left">
              <span class="main-title">随心兑</span>
              <span class="sub-title">| 蛋糕、饮品、甜品</span>
            </div>
            <div class="seeAll" style="padding-right: 4px;" @click="toOtherNext('/orderfood?active=4')">

              <div
                style="background-color: #F99E9B;font-size: 12px; border-radius: 50%;text-align: center;line-height: 22px; width: 18px;height:18px;color: #ffffff;">
                <van-icon name="arrow" />
              </div>
            </div>
          </div>

          <div class="exchange-grid">
            <div class="grid-item" v-for="(item, index) in brandnewlist.slice(0, 10)" :key="item.id">

              <div class="img-box img-box-circle" @click="toSuixd(item.brand_id)">
                <img :src="item.logo" alt="">
              </div>

            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- add 随心兑 -->
    <!--分类-->
    <div class="tabbar" :class="{ tabbar2: show }" ref="topScroll" v-if="tabList.length > 0">
      <div v-if="isLoading" class="isTab" :style="'width:' + tabList.length * 90 + 'px;'"></div>
      <div class="tabbarBox" :style="'width:' + tabList.length * 90 + 'px;'">
        <div class="tabItem" v-for="(item, index) in tabList" :key="item.id" @click="changeTab(item.id, index)">
          <div class="tabTop " :class="{ tabTop2: active == item.id }">{{ item.title }}</div>
          <div class="tabBottom" :class="{ tabBottom2: active == item.id }">{{ item.xtitle }}</div>
        </div>
      </div>
    </div>
    <!--    "cpbs": "2", //1-商城产品 2-蛋糕-->
    <div class="listBox" :style="{ minHeight: isHeight ? 'calc(100vh - 110px)' : '' }" :class="{ fixedHeight: show }">

      <ShopList :productList="productList" :indexs="1"></ShopList>
      <loading style="margin-top: 2px" v-if="isLoading"></loading>
    </div>
    <!--  tabbar-->
    <!--    <NavigationTab :active="0"></NavigationTab>-->
  </div>
</template>
<script>
import NavigationTab from "@/components/NavigationTab.vue";
import { getCakeIndex, getCakeProductList } from "@/api";
import City from "@/components/City.vue";
import ShopList from "@/components/ShopList.vue";
import { getIsShowOder, getIsShowOders, sxfcard, brandnew, ElectronLists } from '@/api/service'
export default {
  name: "IndexView",
  components: {
    ShopList,
    City,
    NavigationTab
  },
  data() {
    return {
      drinkBrandList: [],
      sxfcardfag: '',
      fenl: '',
      gonggao: "",
      flag: 0,
      searchValue: "",
      banneerList: [],
      funList: [],
      xfunList: [],
      show: false,
      isLoading: false,
      active: 0,
      tabList: [],
      isHeight: false,
      cityId: "",
      brandList: [],
      loadingflag: true,
      pageno: 1,
      isScroll: false,
      productList: [],
      isShow: false,
      isCityId: true,
      kefu: "",
      addbanner: '',
      brandnewlist: []
    }
  },
  methods: {
    toSuixd(ids) {
      this.$router.push({ path: "/orderfood", query: { active: 4, brandId: ids } })
    },

    sxfcards() {
      sxfcard().then(res => {
        if (res.code == 200) {
          this.sxfcardfag = res.data.sxf
        }
      })
    },



    toNexts(path) {
      this.$router.push(path)
    },
    getIsShowOder() {
      getIsShowOders().then(res => {
        if (res.code == 200) {
          this.flag = res.data.flag
          // this.flag = 1
          // this.signal = res.data.signal
        }
      })
    },
    tokf() {
      window.location.href = this.kefu
    },
    brandaelectiondetailsadd(vals, id) {
      if (vals == 1) {
        this.$router.push({ path: '/brandselectiondetailsone', query: { classfynum: vals, id: id } })

      } else {
        this.$router.push({ path: '/brandselectiondetailsadd', query: { classfynum: vals, id: id } })

      }
    },
    toOtherdetail(item) {
      window.location.href = item.url
    },
    // 获取
    getCakeIndexList() {
      getCakeIndex().then(res => {
        this.loadingflag = false
        if (res.code == 200) {

          this.drinkBrandList = res.data.zhichong || []
          this.banneerList = res.data.nav
          this.tabList = res.data.classify
          this.gonggao = res.data.gonggao.gonggao
          this.funList = res.data.datu;
          this.fenl = res.data.fenlei;
          console.log(res.data.shuixinduei)
          this.brandnewlist = res.data.shuixinduei



          this.addbanner = res.data.datu.filter(item => item.title == '生日蛋糕')
          this.xfunList = res.data.xiaotu
          this.brandList = res.data.brand
          this.active = this.tabList[0].id
          if (this.banneerList) {
            this.isCityId = false
            this.getCakeProduct(this.active)
          }
        }
      })
    },
    toBrandDetail(id, brandId) {
      // this.$router.push({ path: "/brandDetail", query: { id, brandId: id } })
      this.$router.push({ path: "/directCharge", query: { id, brandId: id } })

    },
    //头部fixed定位
    showSearch() {
      let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
      if (scrollTop > this.$refs.topBox.getBoundingClientRect().height + this.$refs.centerBox.getBoundingClientRect().height) {   // 当页面滚动到高度300px处，动态绑定class 来设置头部固定定位
        this.show = true;
      } else {
        this.show = false;
      }
    },
    onChangeShop() {

    },
    changeTab(id, index) {
      this.isHeight = true
      this.isScroll = false
      this.productList = []
      this.pageno = 1
      this.active = id
      this.isTab = true
      document.documentElement.scrollTop += this.$refs.topScroll.getBoundingClientRect().top
      this.getCakeProduct(this.active)
      if ((index + 1) >= 3) {
        this.$refs.topScroll.scrollLeft = 60 * (index + 1)
      } else {
        this.$refs.topScroll.scrollLeft = 0
      }
    },
    addgo(n) {
      var path = ""
      if (n == 1) {
        path = "/orderfood?active=2"
      }
      else if (n == 2) {
        path = "/orderfood?active=1"
      }
      else {

        path = "/orderfood"
      }
      console.log(this.addbanner[0].banner)
      this.$router.push(path + "?banner=" + this.addbanner[0].banner)
    },
    toNext(item) {
      var path = ""
      if (item.id == 6) {
        path = "/birthdayCake"
      } else if (item.id == 7) {
        path = "/orderfood"
      } else if (item.id == 8) {
        path = "/brandSelection"
      } else if (item.id == 9) {
        path = "/niceBirthday"
      } else if (item.id == 10) {
        path = "/exchangeList"
      } else if (item.id == 11) {
        path = "/afternoonTea"
      } else if (item.id == 17) {
        path = "/orderfood"

      }

      if (item.id == 17) {
        sessionStorage.removeItem("cake")  // 清除 cake 缓存
        this.$router.push(path + "?active=" + 3)
      } else {
        this.$router.push(path + "?banner=" + item.banner)

      }

    },
    toOtherNext(path) {
      this.$router.push(path)
    },
    todetail(item) {
      if (item.cpbs == 1) {
        this.$router.push({ path: "/shopDetail", query: { id: item.id } })
      } else if (item.cpbs == 2) {
        this.$router.push({ path: "/productDetail", query: { id: item.id } })
      }
    },
    getCakeProduct(fid, pageno = this.pageno) {
      this.isLoading = true
      getCakeProductList({
        fid,
        pageno,
        pagesize: 10
      }).then(res => {
        // console.log(res)
        this.isLoading = false
        if (res.code == 200) {
          if (res.data.product_list.length == 0) {
            this.isScroll = true
            return
          }
          res.data.product_list.forEach(item => {
            this.productList.push(item)
          })
        }
      })
    },
    //滚动条事件
    handleScroll(e) {
      let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
      let scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
      let clientHeight = document.documentElement.clientHeight || document.body.clientHeight;
      // console.log(Math.ceil(scrollTop + clientHeight), scrollHeight)
      if (Math.ceil(scrollTop + clientHeight) >= scrollHeight) {
        // console.log("{{{{")
        this.onMost()
      }
    },
    onMost() {
      // console.log(this.isScroll)
      if (!this.isScroll) {
        this.pageno++
        // console.log(this.active,this.pageno)
        this.getCakeProduct(this.active, this.pageno)
      }
    },
    getCityId(e) {
      // console.log(e)
      if (e == "") {
        this.loadingflag = false
        this.isCityId = true
        this.$toast("定位失败，请选择城市")
        return
      }
      this.getCakeIndexList()
    },
    // 提示重新选择
    changeAddress() {
      this.$dialog.confirm({
        message: '定位失败，请选择城市',
        confirmButtonColor: 'red',
        cancelButtonColor: 'red',
        showCancelButton: false
      }).then(() => {
        this.$router.push({ path: "/citylist", query: { city: '' + "" } })
        sessionStorage.setItem("promptcity", "2")
      }).catch(() => {
      })

    },
  },
  created() {
    this.getIsShowOder();
    // this.loadingflag = false
    this.cityId = sessionStorage.getItem("cityId")


    // this.getCakeIndexList()

    this.kefu = localStorage.getItem("kefu")

    this.sxfcards()


    if (sessionStorage.getItem("cityName") && sessionStorage.getItem("cityId")) {
      this.getCakeIndexList()
    }

  },
  watch: {
    // 监听 Vuex 中的 token 变化
    '$store.state.token': {
      handler(newVal) {
        if (newVal) {
          this.sxfcards()
          // 登录成功后，只要有 token，直接强拉接口
          this.getCakeIndexList();

        }
      },
      immediate: false
    },
    // 监听路由（如果你登录后是 replace 回首页）
    $route(to, from) {
      // 如果是从登录页回到首页
      if ((to.path === '/' || to.path === '/index') && from.path !== '/') {
        this.sxfcards()
        // 把刚才的 alert 去掉，改用这里拉接口
        this.getCakeIndexList();

      }
    }
  },
  mounted() {
    this.sxfcards()

    setTimeout(() => {
      if (!sessionStorage.getItem("cityName")) {
        this.loadingflag = false
        this.changeAddress()
      }
    }, 10000)
    window.addEventListener('scroll', this.handleScroll);
    window.addEventListener('scroll', this.showSearch);
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.handleScroll, false);
    window.removeEventListener('scroll', this.showSearch, false);
  },
}
</script>

<style scoped lang="less">
.van-notice-bar {
  margin-top: 8px;
  border-radius: 6px;
}

.location {
  min-height: 100vh;
  background-color: #F0F0F0;
  padding-bottom: 60px;
  box-sizing: border-box;
}

.topBox {
  background-image: linear-gradient(to bottom, #F0BFC2, #F0F0F0);

  //头部
  .topPage {
    padding: 0px 15px;

    .title {
      text-align: center;
      color: white;
      font-size: 14px;
    }

    .address {
      display: flex;
      align-items: center;
      gap: 8px;
      color: white;
    }

    .searchBox {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }


    .searchValue {
      width: 80%;
    }

    .van-search {
      padding-bottom: 6px;
      padding-top: 6px;
      padding-right: 8px;
    }

    .van-search .van-cell {
      background-color: transparent;
    }

    .van-search__content {
      background: rgba(255, 255, 255, 0.8);
      border-radius: 30px;
    }

    /deep/ .van-field__control {
      color: #92716D;
    }

    .customer {
      width: 26px;
    }
  }

  .banner {
    width: 100%;
    padding: 0px 10px;
    box-sizing: border-box;
  }
}

.centerBox {
  padding: 8px 10px;

  .toHome {
    width: 50%;
    position: relative;
  }

  .imgTitle {
    border-radius: 10px;
  }

  .titleBox {
    position: absolute;
    top: 11px;
    left: 10px;

    .title {
      font-weight: bold;
      font-size: 15px;
    }

    .text {
      font-size: 12px;
      color: #675f61;
      padding-top: 2px;
    }
  }

  .flexBox {
    width: 100%;
    display: flex;
    align-items: center;
    gap: 5px;
  }

  .birthdayBox {
    overflow-x: auto;
    overflow-y: hidden;
    margin-top: 8px;
  }

  .birthday {
    display: flex;
    gap: 5px;

    .listItem {
      width: 100%;
    }
  }

  .birthdayBox::-webkit-scrollbar {
    display: none
  }
}

.listBox {
  //padding: 0px 10px;
  //display: flex;
  //justify-content: space-between;
  //flex-wrap: wrap;
  //gap: 5px;
  //margin-top: 12px;
  //align-items: flex-start;

  .listItem {
    width: 49%;
    background-color: #FDD7D6;
    border-radius: 5px;
    padding-bottom: 6px;
    position: relative;

    .topItem {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 5px 10px 3px 0px;

      .titleBack {
        background-image: url("../assets/dangao/x.png");
        width: 108px;
        height: 27px;
        background-size: 100% 100%;
        box-sizing: border-box;
        padding: 0px 15px 1px;
        display: flex;
        color: white;
        align-items: center;
        gap: 6px;
        padding-bottom: 5px;

        .start {
          width: 10px;
        }

        .startTitle {
          font-size: 13px;
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
          width: 80%;
        }
      }

      .burst {
        width: 21px;
        height: 23px;
        background-image: url("../assets/dangao/b.png");
        background-size: 100% 100%;
      }
    }

    .cakeMax {
      padding: 0px 5px;
    }

    .cakeImg {
      width: 100%;
      height: 130px;
      border-top: 1px dashed #DEA1A1;
      padding-top: 4px;
    }

    .cardBack {
      padding: 0px 5px;
      margin-top: 4px;
    }

    .cakeInfo {
      background-color: #ffffff96;
      border-radius: 3px;
      position: relative;
      box-sizing: border-box;
      padding: 5px;

      .cakeNmae {
        font-size: 13px;
        font-weight: bold;
        display: -webkit-box;
        -webkit-box-orient: vertical;
        overflow: hidden;
        -webkit-line-clamp: 1;
        /* 显示两行 */
      }

      .cakeText {
        font-size: 10px;
        color: #646060;
        padding-left: 6px;
        padding-top: 2px;
      }

      .cakeBottom {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding-top: 8px;

        .cakePrice {
          color: #BB3F42;
          font-size: 12px;
          font-weight: bold;
        }

        .cakePrice span {
          font-size: 17px;
        }

        .delivery {
          background-color: #B5595B;
          color: #EAD1D2;
          font-size: 10px;
          padding: 1px 3px;
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;
          max-width: 50px;
          text-align: center;
        }
      }
    }
  }
}

.goAtOnce {
  display: flex;
  align-items: center;
  border-radius: 30px;
  font-size: 10px;
}

.cakeBrand {
  background-color: #FEF4F3;
  margin-top: 9px;
  border-radius: 10px;
  padding: 10px 10px 15px 13px;

  .brandtitleBox {
    display: flex;
    align-items: center;
    justify-content: space-between;

    .title {
      font-size: 15px;
      font-weight: bold;
      display: flex;
      align-items: center;
    }

    .seeAll {
      font-size: 12px;
      color: #8f8a8a;
      display: flex;
      align-items: center;
      gap: 2px;
    }
  }

  // .cakeBoxImg {
  //   display: flex;
  //   align-items: flex-end;
  //   flex-wrap: wrap;
  //   gap: 10px 0px;
  //   margin-top: 10px;

  //   .imgMax2 {
  //     width: 20%;
  //   }

  //   .imgMax {
  //     width: 60px;
  //     height: 60px;
  //     margin: auto;
  //     border-radius: 10px;
  //     overflow: hidden;
  //     margin-right: 10px;

  //     img {
  //       width: 100%;
  //       height: 100%;
  //       object-fit: contain;
  //       /* 保证图片完整显示 */
  //     }
  //   }
  // }
  .cakeBoxImg {
    display: flex;
    align-items: center;
    justify-content: space-between;
    /* 🚨 关键：让这 5 个图标均匀分布在一行 */
    margin-top: 10px;
    padding: 0 2px 0 0;

    .imgMax2 {
      width: 18%;
      /* 🚨 关键：5个的话，每个宽度大概 18% 最合适，留点间隙 */
      display: flex;
      justify-content: center;
    }

    .imgMax {
      width: 60px;
      height: 60px;
      border-radius: 10px;
      overflow: hidden;

      img {
        width: 100%;
        height: 100%;
        object-fit: contain;
        /* 保证图片完整显示 */
      }
    }
  }

  /deep/ .van-swipe__indicator {
    display: initial;
    border-radius: 0px;
    width: 15px;
    height: 2px;
    background-color: #b7b7b7;
    margin: 0;
  }

  /deep/ .van-swipe__indicators {
    bottom: 0px;
    border-radius: 30px;
    overflow: hidden;
  }


}

.tabbar {
  background-color: #fff;
  overflow-x: auto;
  overflow-y: hidden;
  padding: 7px 15px;
  margin-top: 5px;
  position: relative;

  .tabbarBox {
    display: flex;
    align-items: center;
    gap: 20px;
  }

  .tabItem {
    width: 100%;
  }

  .tabTop {
    //font-weight: bold;
    text-align: center;
    font-size: 15px;
  }

  .tabBottom {
    font-size: 12px;
    color: #A8A8A8;
    text-align: center;
    padding-top: 2px;
    margin-top: 3px;
  }

  .tabTop2 {
    color: #240003;
    font-weight: 600;
  }

  .tabBottom2 {
    padding: 2px 4px 3px;
    width: 85%;
    margin: auto;
    margin-top: 3px;
    background-color: #F99E9B;
    color: #ffffff;
    border-radius: 30px;
    font-size: 13px;
  }
}

.tabbar2 {
  position: fixed;
  top: -5px;
  left: 0;
  z-index: 99;
  width: 93%;
}

.tabbar::-webkit-scrollbar {
  display: none
}

.isTab {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

.fixedHeight {
  padding-top: 65px;
}

/deep/ .van-swipe {
  border-radius: 8px;
}

.modelNo {
  background-color: #F0F0F0;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  z-index: 2000;
  margin-top: 6vh;
}

.bad {
  font-size: 13px;
  padding: 10px;
}

.bgadd {
  background-color: #F99E9B;
  color: #ffffff;
  font-size: 12px;
  font-weight: normal;
  padding: 2px 8px 2px;
  border-radius: 30px;
  margin: 0 10px;
}

.bgadds {
  border: 1px solid #F99E9B;
  color: #F99E9B;
  font-size: 12px;
  font-weight: normal;
  padding: 1px 16px 2px;
  border-radius: 30px;
}

// suixn
/* 随心兑 模块样式 */
.exchange-section {
  padding: 10px;
  margin-top: -10px;
}

.exchange-card {
  background-color: #FFF1F1;
  border-radius: 12px;
  padding: 10px 8px 12px 8px;
  box-sizing: border-box;

  .exchange-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;

    .header-left {
      padding: 0 5px;
      display: flex;
      align-items: baseline;

      .main-title {
        font-size: 15px;
        font-weight: bold;
        color: #000000;
      }

      .sub-title {
        font-size: 12px;
        color: #666;
        margin-left: 6px;
      }
    }

    .header-right {
      width: 24px;
      height: 24px;
      background-color: #F99E9B;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #fff;
      font-size: 12px;
    }
  }

  .exchange-grid {
    display: flex;
    flex-wrap: wrap;
    /* 改为 flex-start 左对齐，防止第二行元素分散 */
    justify-content: flex-start;
    /* 上下间距 12px，左右间距 0（由宽度计算控制） */
    row-gap: 12px;
    column-gap: 0;

    .grid-item {
      /* 一行5个：100% / 5 = 20% */
      width: 20%;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;

      .img-box {
        /* 宽度占满 20% 的格子，并扣除左右边距 */
        width: 85%;
        aspect-ratio: 1 / 1;
        background-color: transparent;
        /* 默认透明，防止有底色 */
        display: flex;
        align-items: center;
        justify-content: center;
        box-sizing: border-box;

        img {
          width: 100%;
          height: 100%;
          /* 如果原图是圆形的，用 contain 不裁剪；如果原图是方形带白底，用 cover 裁剪成圆形 */
          object-fit: contain;
        }
      }

      /* 针对你的截图，强制变成圆形 */
      .img-box-circle {
        background-color: #fff;
        border-radius: 8px;
        /* 变成圆形 */
        overflow: hidden;
        padding: 0;
        /* 原图自带背景，不需要内边距 */
        box-shadow: none;
        /* 去除阴影，因为你截图里的原图自带立体感 */

        img {
          /* 如果原图是正方形，强制铺满并裁剪成圆形 */
          object-fit: cover;
        }
      }
    }
  }
}

// sunuisx
</style>
