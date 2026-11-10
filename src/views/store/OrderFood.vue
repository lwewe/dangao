<template>
  <div class="orderfood">

    <NProgress v-if="loadingflag" />
    <!--    tab切换-->
    <div class="tabListBox">
      <div
        v-for="item in cake == 88 ? tabList.filter(item => item.active == 2) : flag == 0 ? tabList : flag == 2 ? tabList.filter(item => item.active != 3 && item.active != 2) : tabList.filter(item => item.active != 3)"
        :key="item.active" :style="{ backgroundImage: item.active == active ? 'url(' + item.back + ')' : '' }"
        class="tabItem" @click="changeType(item.active)"
        :class="{ tabItem2: item.active == active, tabItem3: cake ? cake != 2 : false, tabItem4: item.active == active && cake != 1 || types == 1, }"
        v-if="item.isShow">
        <div>{{ item.title }}</div>
        <div class="tabIcon" v-if="item.active == active">
          <img class="img" src="../../assets/zhichong/xuanz.png" alt="">
        </div>
      </div>
    </div>
    <!--    自助点餐-->
    <div class="leftwrap" v-if="active == 3 && flag == 0">

      <img src="../../assets/newico/tp.png" alt="" style="width: 100%;">
      <!-- <div style="margin-top: 5px;padding: 0 10px;" @click="toNexts()">
        <van-notice-bar :text="gonggao" left-icon="volume-o" />
      </div> -->

      <div class="pd0">
        <div class="banner1" v-for="(item, index) in imgurlbox" :key="item.id" v-if="item.id != 25"
          @click="goindex(index + 1, index <= 2 || index == 6 ? 1 : 2)">
          <img class="img" :src="item.img" alt="">
        </div>
      </div>


    </div>
    <!--    电子券-->
    <div class="rightwrap" v-if="active == 1">


      <ElectronicCoupon></ElectronicCoupon>
    </div>

    <div class="brandList" v-if="active == 2 && (flag != 2 || cake == 88)">
      <div v-for="item in brandList" :key="item.id" class="brandItem" @click="todetail(item.id)">
        <img class="img" :src="item.img" alt="">
      </div>
    </div>

    <!-- 随心兑 -->


    <div v-if="active == 4" class="exchange-wrap">

      <!-- 1. 顶部搜索框 -->
      <div class="search-header">
        <div class="search-box">
          <van-icon name="search" class="search-icon" />
          <input type="text" v-model="searchKeyword" placeholder="输入门店名称" class="search-input"
            @keyup.enter="onSearch" />
        </div>
      </div>

      <!-- 2. 品牌区域 (相对定位，用于包裹横滑和下拉面板) -->
      <div class="brand-section">
        <!-- 2.1 横滑区域 -->
        <div class="brand-scroll-wrap">
          <!-- 品牌列表 -->
          <!-- <div class="brand-item" v-for="brand in exchangeBrands" :key="brand.id" :id="'brand-' + brand.id"
            :class="{ active: currentBrandId === brand.id }" @click="selectBrand(brand, brand.id)">
            <img :src="brand.logo || brand.img" alt="" class="brand-logo">
            <div class="brand-name">{{ brand.name || brand.title }}</div>
          </div> -->
          <div class="brand-item" v-for="(brand, index) in exchangeBrands" :key="brand.id" :id="'brand-' + brand.id"
            :class="{ active: currentBrandId === brand.id }" :style="{ backgroundImage: getBrandGradient(index) }"
            @click="selectBrand(brand, brand.id)">
            <div class="brand-inner">
              <img :src="brand.logo || brand.img" alt="" class="brand-logo">
              <div class="brand-name">{{ brand.name || brand.title }}</div>
            </div>
          </div>
          <!-- 右侧固定的“全部”按钮 -->
          <div class="brand-all-btn" :class="{ active: showAllBrands || currentBrandId === 0 }"
            @click="toggleAllBrands">
            全部 <van-icon :name="showAllBrands ? 'arrow-up' : 'arrow-down'" />
          </div>
        </div>

        <!-- 2.2 点击“全部”后在当前区域展开的面板 -->
        <div class="all-brand-panel" v-show="showAllBrands">
          <div class="all-brand-grid">
            <div class="grid-brand" v-for="brand in exchangeBrands" :key="'all-' + brand.id"
              @click="selectBrand(brand, brand.id)">
              <img :src="brand.logo || brand.img" alt="">
              <div class="grid-brand-name">{{ brand.name || brand.title }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- 3. 下方的门店/商品列表 -->
      <!-- 3. 下方的门店/商品列表 -->
      <div class="shop-list">
        <!-- 状态 1：首次进入或切换品牌时的加载中（覆盖全屏） -->
        <div v-if="isShopLoading && !isLoadMore" style="text-align: center; color: #999; padding: 40px 0;">
          <van-loading size="24" vertical style="margin: auto;">正在加载...</van-loading>
        </div>

        <!-- 状态 2：加载完毕且没有数据 -->
        <div v-else-if="!isShopLoading && exchangeShopList.length === 0"
          style="text-align: center; font-size: 12px; color: #999; padding: 40px 0;">

          <p>没有找到相关门店</p>
        </div>

        <!-- 状态 3：有数据 -->
        <div v-else class="shop-card" v-for="(item, index) in exchangeShopList" :key="item.id || index">
          <div class="shop-logo">

            <img :src="logoImg" alt="">
          </div>
          <div class="shop-info">
            <div class="shop-title-row">
              <div class="shop-name">{{ item.name }}</div>
              <div class="shop-distance" v-if="item.distance != null && item.distance !== ''">
                距您 {{
                  Number(item.distance) >= 1000
                    ? (Number(item.distance) / 1000).toFixed(2) + 'km'
                    : Number(item.distance).toFixed(0) + 'm'
                }}
              </div>
            </div>
            <div class="shop-address"><van-icon name="location-o" /> {{ item.address }}</div>
            <div class="shop-tags">
              <span class="tag">到店兑换</span>
            </div>
            <div style="text-align: right;" @click="goExchange(item)"><span class="shop-btn">去兑换</span></div>
          </div>

        </div>

        <!-- 状态 4：底部上拉加载更多的提示 -->
        <div v-if="isLoadMore" style="text-align: center; color: #999; padding: 10px 0; font-size: 12px;">
          <van-loading size="16"
            style="display: inline-block; vertical-align: middle; margin-right: 4px;">加载中...</van-loading>
        </div>
      </div>

    </div>
    <!-- 随心兑换 -->


  </div>
</template>

<script>
import { getWxLocation } from '@/utils/wxLocation'
import { getBannerList, getBannerLists, getIsShowOder, sxfcard, brandnew, DangaoStore } from '@/api/service'
import ElectronicCoupon from "@/components/select/ElectronicCoupon.vue";
import { getBrandDirect } from "@/api/brand";
export default {
  components: { ElectronicCoupon },
  data() {
    return {
      logoImg: '',
      sxfcardfag: '',
      shopPageNo: 1,
      isShopLoading: false, isLoadMore: false, isFetching: false,
      showAd: true,
      gonggao: '',
      active: 1,
      imgurlbox: [],
      loadingflag: true,
      cake: 0,
      types: 0,
      tabList: [
        {
          title: "电子券",
          active: 1, isShow: false,
          back: require("../../assets/zhichong/yb.png")
        },
        {
          title: "随心兑", isShow: false,
          active: 4,
          back: require("../../assets/zhichong/yb.png")
        },

        {
          title: "饮品直冲", isShow: false,
          active: 2,
          back: require("../../assets/zhichong/zj.png")
        },
        {
          title: "自助点餐", isShow: false,
          active: 3,
          back: require("../../assets/zhichong/zb.png")
        },
      ],
      pageno: 1,
      brandList: [],
      isScroll: false,
      flag: 0,
      signal: 0,

      showAllBrands: false,
      searchKeyword: "",       // 搜索关键词
      currentBrandId: 0,       // 当前选中的品牌ID，0代表全部
      showAllBrands: false,    // 是否展示全屏品牌面板
      exchangeBrands: [        // 临时模拟的品牌数据，你需要换成接口获取的
      ], isInitExchangeRunning: false,
      exchangeShopList: [      // 临时模拟的下方门店数据

      ],

    };
  },
  methods: {
    getCleanActive() {
      let active = this.$route.query.active || '1';
      if (typeof active === 'string') {
        active = active.split('?')[0];
      }
      return Number(active) || 1;
    },

    // 2. 统一初始化所有 Tab 接口的入口 (核心驱动)
    initTabData() {
      if (this.active == 2) {
        this.brandList = [];
        this.pageno = 1;
        this.getBrandDirect();
      } else if (this.active == 3) {
        this.showAd = true;
        this.getlistpics();
      } else if (this.active == 4) {
        if (this.exchangeBrands && this.exchangeBrands.length > 0) {
          this.initExchangeData();
        }
      } else if (this.active == 1) {
        setTimeout(() => {
          this.loadingflag = false;
        }, 500);
      }
    },
    getBrandGradient(index) {
      const gradients = [
        'linear-gradient(to bottom, #E45FB9, #F1608A, #FB6768)',
        'linear-gradient(to bottom, #F8E59B, #FAC67D, #FD9C59)',
        'linear-gradient(to bottom, #A9D8E6, #73A5EC, #5582F2)',
        'linear-gradient(to bottom, #FFD5B3, #F793BA, #F05CC3)',
      ]
      return gradients[index % 4]
    },
    // 计算两点距离（单位：米）
    getDistance(lat1, lng1, lat2, lng2) {
      if (!lat1 || !lng1 || !lat2 || !lng2) return null;
      const rad = Math.PI / 180;
      const R = 6371000; // 地球半径
      const dLat = (lat2 - lat1) * rad;
      const dLng = (lng2 - lng1) * rad;
      const a = Math.sin(dLat / 2) ** 2 +
        Math.cos(lat1 * rad) * Math.cos(lat2 * rad) * Math.sin(dLng / 2) ** 2;
      return Math.round(2 * R * Math.asin(Math.sqrt(a))); // 返回整数米
    },
    scrollToActiveBrand(brandId) {
      if (!brandId) return;
      this.$nextTick(() => {
        const container = document.querySelector('.brand-scroll-wrap');
        const target = document.getElementById('brand-' + brandId);

        if (container && target) {
          // 计算目标元素相对于滚动容器的偏移量
          const offsetLeft = target.offsetLeft;
          // 让容器的滚动条平滑滚动到目标位置
          // 为了看起来居中，稍微减去一点偏移（比如减去 100px）
          container.scrollTo({
            left: offsetLeft - 100,
            behavior: 'smooth' // 平滑滚动
          });
        }
      });
    },
    initExchangeData() {
      if (!this.exchangeBrands || this.exchangeBrands.length === 0) return;

      // 🚨 核心拦截：如果正在定位中，直接 return
      if (this.isInitExchangeRunning) return;
      this.isInitExchangeRunning = true;

      const urlBrandId = this.$route.query.brandId;
      const targetBrandId = urlBrandId || this.exchangeBrands[0].id;

      this.currentBrandId = targetBrandId;
      const currentBrandObj = this.exchangeBrands.find(b => b.id == targetBrandId);
      if (currentBrandObj) this.logoImg = currentBrandObj.logo;

      this.scrollToActiveBrand(targetBrandId);

      // 先把列表清空，防止用户看到旧数据
      this.exchangeShopList = [];
      this.isShopLoading = true;

      getWxLocation()
        .then(loc => {
          if (loc.city) sessionStorage.setItem('cityName', loc.city);
          sessionStorage.setItem('longitude', loc.longitude);
          sessionStorage.setItem('latitude', loc.latitude);
          this.fetchExchangeShopList(targetBrandId, this.searchKeyword, false, loc.latitude, loc.longitude);
        })
        .catch(() => {
          this.fetchExchangeShopList(targetBrandId, this.searchKeyword);
        })
        .finally(() => {
          // 🚨 无论成功失败，都要释放锁
          this.isInitExchangeRunning = false;
        });
    },
    brandnews() {
      return brandnew().then(res => {
        if (res.code == 200 && res.data) {
          this.exchangeBrands = res.data.map(item => ({
            id: item.brand_id,
            name: item.name,
            logo: item.logo
          }));

          if (this.exchangeBrands.length > 0) {
            this.logoImg = this.exchangeBrands[0].logo;
          }

          // 🚨 只有条件满足时，才去拉取门店
          if (this.active == 4) {
            this.initExchangeData();
          }
        }
      });
    },
    updateTabShow() {
      this.tabList.forEach(item => {
        // 🚨 核心：随心兑 (active == 4) 独立判断，只要 sxf == 1 就显示
        if (item.active == 4) {
          item.isShow = (this.sxfcardfag == 1);
          return; // 直接 return，不走下面老逻辑，完美兼容
        }

        // 下面是原有的老逻辑，原封不动保留
        if (this.cake == 88) {
          item.isShow = item.active == 2;
        } else if (item.active == 1 || item.active == 2) {
          item.isShow = this.cake != 2;
        } else {
          item.isShow = this.cake != 1 || this.types == 1;
        }
      });
    },
    // updateTabShow() {
    //   // 🚨 核心：如果接口没权限或报错，所有 Tab 强制关闭，直接 return
    //   if (this.sxfcardfag != 1) {
    //     this.tabList.forEach(item => {
    //       item.isShow = false;
    //     });
    //     return;
    //   }

    //   // 如果接口通过了（有权限），再执行你原本的显示逻辑
    //   this.tabList.forEach(item => {
    //     if (item.active == 4) {
    //       item.isShow = true; // 随心兑
    //     } else if (this.cake == 88) {
    //       item.isShow = item.active == 2;
    //     } else if (item.active == 1 || item.active == 2) {
    //       item.isShow = this.cake != 2;
    //     } else {
    //       item.isShow = this.cake != 1 || this.types == 1;
    //     }
    //   })
    // },
    sxfcards() {
      sxfcard()
        .then(res => {
          this.sxfcardfag = (res.code == 200 && res.data) ? res.data.sxf : '';
          this.updateTabShow();

          // 🚨 修复：去掉路由跳转，直接改状态和地址栏
          if (this.active == 4 && this.sxfcardfag != 1) {
            this.active = 1;
            window.history.replaceState(null, '', '/#/orderfood?active=1');
          }
        })
        .catch(() => {
          this.sxfcardfag = '';
          this.updateTabShow();
          if (this.active == 4) {
            this.active = 1;
            window.history.replaceState(null, '', '/#/orderfood?active=1');
          }
        });
    },
    toggleAllBrands() {
      this.showAllBrands = !this.showAllBrands;
    },
    selectBrand(val, id) {
      console.log(val)
      this.logoImg = val.logo
      this.currentBrandId = id;
      this.showAllBrands = false; // 选完品牌后收起面板
      this.searchKeyword = "";
      window.scrollTo(0, 0);
      this.isScroll = false;
      const lat = sessionStorage.getItem('latitude') || '';
      const lng = sessionStorage.getItem('longitude') || '';
      this.fetchExchangeShopList(id, this.searchKeyword, false, lat, lng);
      // this.fetchExchangeShopList(id, this.searchKeyword);
    },


    goExchange(item) {
      this.$router.push({
        path: '/GetpayqrCode', // 你的付款码页面路由
        query: {
          brandIds:this.currentBrandId,
          brandId: item.id, // 如果有品牌ID就用，没有就用当前选中的
          shopId: item.shopId,
          brandName: item.brandName || '好利来',        // 品牌名称
          shopName: item.name,                         // 门店名称
          address: item.address,                       // 门店地址
          distance: item.distance,                     // 距离
          logo: this.logoImg                           // 🚨 把当前选中的品牌 LOGO 传过去
        }
      })
    },


    // 搜索逻辑
    onSearch() {
      // console.log("正在搜索：", this.searchKeyword);
      // this.fetchExchangeShopList(this.currentBrandId, this.searchKeyword);
      const lat = sessionStorage.getItem('latitude') || '';
      const lng = sessionStorage.getItem('longitude') || '';
      this.fetchExchangeShopList(this.currentBrandId, this.searchKeyword, false, lat, lng);

    },

    // 获取下方门店列表
    fetchExchangeShopList(brandId = '', keyword = '', append = false, lat = '', lng = '') {
      // 🚨 核心拦截：如果当前正在发请求，直接 return，不执行下面的逻辑
      // if (this.isFetching) return;
      if (this.isFetching && append) return;
      // this.isFetching = true;

      if (!append) {
        this.isFetching = true;
        this.exchangeShopList = [];
        this.shopPageNo = 1;
        this.isScroll = false;
        this.isShopLoading = true;
      } else {
        this.isFetching = true;
        this.isLoadMore = true;
      }

      const params = {
        pageNo: this.shopPageNo,
        pageSize: 10,
        brandId: brandId || '',
        shopName: keyword || '',
        latitude: lat || '',
        longitude: lng || '',
        city: sessionStorage.getItem('cityName') || '',
      };

      DangaoStore(params).then(res => {
        if (res.code == 200 && res.data) {
          let rows = res.data.rows || [];

          // 🚨 核心修改：拿到当前缓存的用户经纬度，遍历计算距离
          const userLat = sessionStorage.getItem('latitude');
          const userLng = sessionStorage.getItem('longitude');

          if (userLat && userLng) {
            rows.forEach(item => {
              // 如果后端没给，并且门店有经纬度，前端自己算
              if (item.distance === null || item.distance === undefined) {
                if (item.lat && item.lng) {
                  item.distance = this.getDistance(
                    Number(userLat), Number(userLng),
                    Number(item.lat), Number(item.lng)
                  );
                }
              }
            });
          }

          if (rows.length < 10) {
            this.isScroll = true; // 无更多数据
          }

          if (append) {
            this.exchangeShopList = this.exchangeShopList.concat(rows);
          } else {
            this.exchangeShopList = rows;
          }
        } else {
          if (!append) this.exchangeShopList = [];
          this.isScroll = true;
        }
      }).catch(() => {
        if (!append) this.exchangeShopList = [];
        this.$toast('获取门店列表失败');
      }).finally(() => {
        this.isFetching = false;
        this.isShopLoading = false;
        this.isLoadMore = false;
      });
    },
    // 关闭广告
    closeImg() { this.showAd = false; },
    closeAd() {
      this.showAd = false;

    },

    // 点击广告图片
    toNexts() {
      window.location.href = 'http://yxfmm.bjyxfl.com/imgs/ad2.png';
    },
    getIsShowOder() {
      getIsShowOder().then(res => {
        if (res.code == 200) {
          this.flag = res.data.flag
          this.signal = res.data.signal
        }
      })
    },
    todetail(id) {
      this.$router.push({ path: "/directCharge", query: { id } })
    },
    changeType(e) {
      if (this.active == e) return; // 重复点击不触发
      // this.active = Number(e);

      // if (this.active == 2) {
      //   this.brandList = []
      //   this.pageno = 1
      //   this.getBrandDirect()
      // } else if (this.active == 3) {
      //   this.showAd = true
      //   this.getlistpics()
      // } else if (this.active == 4) {
      //   // 如果点随心兑，且已经有品牌数据，立刻初始化
      //   if (this.exchangeBrands.length > 0) {
      //     this.initExchangeData();
      //   }
      // }

      // 更新路由（保留这行即可，会自动把上一页带过来的脏参数覆盖掉）
      this.$router.replace({ path: "/orderfood", query: { active: e } }).catch(() => { });
    },
    getlistpic() {
      getBannerList({
        flag: '1',
        // type:'mdl'
      }).then(res => {
        this.loadingflag = false
        // console.log(res);
        if (res.code == 200) {
          this.imgurlbox = res.data.list
          // console.log(this.imgurlbox);
        }

      })
    },
    getlistpics() {
      getBannerLists({
        flag: '1',
        // type:'mdl'
      }).then(res => {
        this.loadingflag = false
        console.log(res);
        if (res.code == 200) {
          this.imgurlbox = res.data.list

        }

      })
    },
    goindex(num1, num2) {
      // if(num1==4){
      //   this.$toast("正在建设中...,敬请期待")
      //   return
      // }
      this.$router.push({ path: "/select", query: { num1: num1, num2: num2 } })
    },
    getBrandDirect(pageno = this.pageno) {
      getBrandDirect({
        pageno,
        pagesize: 10
      }).then(res => {
        this.loadingflag = false
        if (res.code == 200) {
          // 🚨 如果这是第一页，且没有数据，直接置空
          if (res.data.length == 0) {
            this.isScroll = true
            return
          }

          // 🚨 核心修复：不要用 push，用 concat 整体合并，强制 Vue 刷新视图
          if (pageno === 1) {
            this.brandList = res.data;
          } else {
            this.brandList = this.brandList.concat(res.data);
          }
        }
      })
    },
    //滚动条事件
    handleScroll(e) {
      let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
      let scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
      let clientHeight = document.documentElement.clientHeight || document.body.clientHeight;
      // if (Math.ceil(scrollTop + clientHeight) >= scrollHeight) {
      //   this.onMost()
      // }

      if (Math.ceil(scrollTop + clientHeight) >= scrollHeight - 100) {
        this.onMost()
      }



    },
    onMost() {
      // 🚨 修复核心：只要正在发请求，或者已经到底了，直接 return 阻断，什么都不做！
      if (this.isFetching || this.isScroll) return;

      if (this.active == 2) {
        this.pageno++
        this.getBrandDirect(this.pageno)
      } else if (this.active == 4) {
        // 列表为空时也不允许加载下一页
        if (this.exchangeShopList.length === 0) return;

        this.shopPageNo++
        this.fetchExchangeShopList(this.currentBrandId, this.searchKeyword, true);
      }
    },

  },
  created() {
    this.loadingflag = false;
    this.getIsShowOder();

    if (sessionStorage.getItem("cake")) {
      this.cake = sessionStorage.getItem("cake");
    }
    if (sessionStorage.getItem("types")) {
      this.types = sessionStorage.getItem("types");
    }

    // 🚨 核心修复：使用工具方法获取干净的 active
    this.active = this.getCleanActive();

    if (this.cake == 2) this.active = 3;
    if (this.cake == 88) this.active = 2;

    this.updateTabShow();
  },
  watch: {
    '$route'(to, from) {
      if (to.path === '/orderfood') {
        // 🚨 使用工具方法获取干净的 active
        const newActive = this.getCleanActive();

        // 1. 如果 active 变了
        if (this.active !== newActive) {
          this.active = newActive;
          this.initTabData(); // 只要一变，统一交给 initTabData 去分发请求
        }
        // 2. 如果还在当前 Tab 4，但是 brandId 变了
        else if (this.active === 4 && to.query.brandId !== from.query.brandId) {
          this.initExchangeData();
        }
      }
    }
  },
  mounted() {
    this.sxfcards();
    this.brandnews(); // 只拉品牌数据，内部会判断要不要拉门店

    // 🚨 核心修复：统一在这里发请求，确保 this.active 的赋值已经稳定
    this.initTabData();

    if (this.active != 2 && this.active != 3 && this.active != 4) {
      this.loadingflag = false;
    }

    window.addEventListener('scroll', this.handleScroll);
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.handleScroll, false);
  },
};
</script>
<style>
.shop-address .van-icon {
  color: #EB6A73;
  position: relative;
  top: 2px;
  font-weight: bold;
}
</style>
<style scoped lang="less">
// 悬浮广告样式 // yishagn以上控制广告



// yishagn以上控制广告
.orderfood {
  min-height: 100vh;
  background: #f0f0f0;
  padding-top: 10px;
  box-sizing: border-box;

}

// .leftwrap {
//   padding: 0px 10px 10px;

//   .banner1 {
//     margin-top: 10px;
//   }
// }
.leftwrap {
  width: 100%;

  .pd0 {
    margin-top: 2px;
    padding: 0px 10px 50px 10px;
    display: flex;
    // gap: 7px;
    justify-content: space-between;
    flex-wrap: wrap;
  }

  .banner1 {
    width: 49%;
    margin-top: 5px;
  }
}

.tabListBox {
  background-image: url("../../assets/zhichong/jbback.png");
  background-size: 100% 100%;
  width: 100%;
  height: 44px;
  display: flex;
  align-items: flex-end;
  justify-content: space-between;

  .tabItem {
    width: 33.3%;
    width: 50%;
    text-align: center;
    background-size: 100% 100%;
    font-size: 14px;
    color: #646166;
    height: 54px;
    line-height: 64px;
  }

  .tabItem2 {
    line-height: 54px;
    font-size: 15px;
    font-weight: bold;
    color: #343434;
  }

  .tabItem3 {
    width: 50%;
  }

  //.tabItem4{
  //  margin: 0 auto;
  //}
  .tabIcon {
    width: 16px;
    margin: auto;
    margin-top: -13px;
  }
}

.brandList {
  display: flex;
  flex-wrap: wrap;
  padding: 10px;
  gap: 10px 2px;
  justify-content: space-between;

  .brandItem {
    width: calc(50% - 2px);
  }
}

// suixindui
/* 随心兑模块样式 */
.exchange-wrap {
  background: #f0f0f0;
  min-height: calc(100vh - 54px);
  position: relative;
  padding-bottom: 20px;
}

/* 顶部搜索 */
.search-header {
  padding: 10px 15px;
  background: #fff;

  .search-box {
    display: flex;
    align-items: center;
    background: #f5f5f5;
    border-radius: 20px;
    padding: 6px 12px;

    .search-icon {
      color: #999;
      font-size: 16px;
      margin-right: 6px;
    }

    .search-input {
      border: none;
      background: transparent;
      flex: 1;
      outline: none;
      font-size: 14px;
    }
  }
}

/* 品牌区域包裹层 */
.brand-section {
  position: relative;
  background: #fff;
}

/* 品牌横滑 */
.brand-scroll-wrap {
  display: flex;
  overflow-x: auto;
  padding: 10px 15px;
  gap: 15px;
  align-items: center;
  border-bottom: 1px solid #eee;
  /* 右侧留出“全部”按钮的空间 */
  padding-right: 60px;

  &::-webkit-scrollbar {
    display: none;
  }

  .brand-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
    position: relative;
    border-radius: 10px;
    padding: 3px;
    width: 62px;
    box-sizing: border-box;
    /* 关键：去掉原来的 background-color: #fff */
    background-color: transparent;

    .brand-inner {

      border-radius: 8px;
      width: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 4px 0 3px;
      box-sizing: border-box;
    }

    .brand-logo {
      width: 46px;
      height: 46px;
      border-radius: 8px;
      object-fit: contain;
      margin-bottom: 3px;
    }

    .brand-name {
      font-size: 11px;
      color: #ffffff;
      text-align: center;
      width: 100%;
      padding: 0 2px;
      box-sizing: border-box;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    /* 选中态：保留原来的文字变色，再加一个白边高亮，避免被渐变盖住 */
    &.active {
      .brand-name {
        color: #ffffff;

      }

      .brand-inner {
        box-shadow: 0 0 0 2px #ffffff inset;
      }
    }
  }
}

/* 右侧悬浮的“全部”按钮 */
.brand-all-btn {
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  width: 60px;
  background: linear-gradient(to right, rgba(255, 255, 255, 0.8), #fff 40%);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 13px;
  color: #333;
  z-index: 10;
  border-bottom: 1px solid #eee;
  cursor: pointer;

  .van-icon {
    margin-left: 2px;
    font-size: 12px;
  }

  &.active {
    color: #F99E9B;
    font-weight: bold;
  }
}

/* 在当前页面展开的面板 */
.all-brand-panel {
  position: absolute;
  top: 100%;
  /* 紧贴在横滑条下方 */
  left: 0;
  width: 100%;
  background: #fff;
  z-index: 99;
  padding: 15px;
  box-sizing: border-box;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 0 0 10px 10px;

  .all-brand-grid {
    display: flex;
    flex-wrap: wrap;
    row-gap: 15px;
    max-height: 50vh;
    /* 限制最大高度，超出可滑动 */
    overflow-y: auto;

    .grid-brand {
      width: 20%;
      /* 一行5个 */
      display: flex;
      flex-direction: column;
      align-items: center;

      img {
        width: 45px;
        height: 45px;
        border-radius: 8px;
        border: 1px solid #eee;
        object-fit: contain;
        margin-bottom: 5px;
      }

      .grid-brand-name {
        font-size: 12px;
        color: #333;
        text-align: center;


        width: 100%;
        /* 占满 20% 格子的宽度 */

        box-sizing: border-box;
        /* 让 padding 不影响宽度计算 */
        white-space: nowrap;
        /* 强制不换行 */
        overflow: hidden;
        /* 超出部分隐藏 */
        text-overflow: ellipsis;
        /* 超出部分显示省略号... */


      }
    }
  }
}

/* 底部列表 */
.shop-list {
  padding: 10px;
  /* 如果展开面板很高，可以给列表加个遮罩或让出空间，这里默认不处理 */

  .shop-card {
    background: #fff;
    border-radius: 10px;
    padding: 15px;
    margin-bottom: 10px;
    display: flex;
    align-items: center;

    .shop-logo {
      width: 76px;
      height: 76px;


      margin-right: 12px;

      img {
        border-radius: 8px;
        width: 100%;
        height: 100%;
        object-fit: contain;
      }
    }

    .shop-info {
      flex: 1;
      min-width: 0;

      .shop-title-row {
        display: flex;
        justify-content: space-between;
        align-items: center;

        .shop-name {
          font-size: 14px;
          font-weight: bold;
          color: #333;
          max-width: 150px;
          white-space: nowrap;
          /* 强制一行 */
          overflow: hidden;
          /* 超出隐藏 */
          text-overflow: ellipsis;
          /* 多出来显示 ... */
        }

        .shop-distance {
          font-size: 12px;
          color: #999;
        }
      }

      .shop-address {
        font-size: 12px;
        color: #666;
        margin-top: 5px;
        white-space: nowrap;
        /* 强制一行 */
        overflow: hidden;
        /* 超出隐藏 */
        text-overflow: ellipsis;
        /* 多出来显示 ... */
      }

      .shop-tags {
        margin-top: 2px;

        .tag {
          font-size: 10px;
          color: #FF7D2C;
          border: 1px solid #FF7D2C;
          background-color: #FEECDB;
          padding: 2px 5px;
          border-radius: 4px;
        }
      }
    }

    .shop-btn {
      background: #3FA6FF;
      color: #fff;
      font-size: 12px;
      padding: 4px 16px;
      border-radius: 15px;

    }
  }
}


//suixindui
</style>
