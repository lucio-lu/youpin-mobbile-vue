<template>
  <div class="shopcar-container">
    <div class="goods-list">
      <!-- 商品列表区 -->
      <div class="mui-card" v-for="(item,i) in goodsList" :key="item.id">
        <div class="mui-card-content">
          <div class="mui-card-content-inner">
            <!-- https://mint-ui.github.io/docs/#/zh-cn2/switch -->
            <mt-switch
              v-model="$store.getters.getGoodsSelected[item.id]"
              @change="selectedChanged(item.id, $store.getters.getGoodsSelected[item.id])"
            ></mt-switch>
            <img :src="item.thumb_path" />
            <div class="info">
              <h1>{{ item.title }}</h1>
              <p>
                <span class="price">￥{{ item.sell_price }}</span>
                <numbox
                  max="100"
                  :initvalue="$store.getters.getGoodsCount[item.id]"
                  :goodsid="item.id"
                ></numbox>
                <a href="#" @click.prevent="remove(item.id,i)">删除</a>
              </p>
            </div>
          </div>
        </div>
      </div>

      <!-- 结算区域 -->
      <div class="mui-card">
        <div class="mui-card-content">
          <div class="mui-card-content-inner jiesuan">
            <div class="left">
              <p>总计（不含运费）</p>
              <p>
                已勾选商品
                <span class="red">{{ $store.getters.getGoodsCountAndAmout.count }}</span> 件，总价
                <span class="red">￥ {{ $store.getters.getGoodsCountAndAmout.amount }}</span>
              </p>
            </div>
            <mt-button type="danger">去结算</mt-button>
          </div>
        </div>
      </div>

      <!-- end -->
    </div>
  </div>
</template>

<script>
import { Toast } from "mint-ui";
import numbox from "../subcomponents/shopcar_numbox.vue";

export default {
  data() {
    return {
      goodsList: []
    };
  },
  created() {
    this.getGoodsList();
  },
  methods: {
    getGoodsList() {
      let ids = [];
      this.$store.state.car.forEach(element => {
        ids.push(element.id);
      });
      if (ids.length <= 0) return;
      this.$http
        .get("api/goods/getshopcarlist/" + ids.join(","))
        .then(result => {
          if (result.body.status === 0) {
            this.goodsList = result.body.message;
            console.log(this.goodsList);
          } else {
            Toast("加载购物车列表失败");
          }
        });
    }, //
    remove(id, index) {
      this.goodsList.splice(index, 1);
      this.$store.commit("removeFromCar", id);
    }, //
    selectedChanged(id, val) {
      this.$store.commit("updateGoodsSelected", { id: id, selected: val });
    }
  },
  components: {
    numbox
  }
};
</script>

<style lang="scss" scoped>
.shopcar-container {
  background-color: #eee;
  overflow: hidden;

  .goods-list {
    .mui-card-content-inner {
      display: flex;
      align-items: center;
    }
    img {
      width: 60px;
      height: 60px;
    }
    h1 {
      font-size: 13px;
    }
    .info {
      display: flex;
      flex-direction: column;
      justify-content: space-between;

      .price {
        color: red;
        font-weight: blod;
      }
    }
  }

  .jiesuan {
    display: flex;
    justify-content: space-between;
    align-items: center;
    .red {
      color: red;
      font-weight: bold;
      font-size: 16px;
    }
  }
}
</style>