<template>
  <div class="content">
    <div class="swiper-container">
      <div class="swiper-wrapper">
        <div class="swiper-slide" :style="'background-image: url('+top[0].pic1+')'" @click="goDetail(top[0])"></div>
        <div class="swiper-slide" :style="'background-image: url('+top[1].pic1+')'" @click="goDetail(top[1])"></div>
        <div class="swiper-slide" :style="'background-image: url('+top[2].pic1+')'" @click="goDetail(top[2])"></div>
      </div>
      <div class="swiper-pagination"></div>
      <!--<div class="swiper-button-prev"></div>-->
      <!--<div class="swiper-button-next"></div>-->
    </div>
    <div class="search-text">
      <input type="text" placeholder="请输入搜索内容" v-model="keyword">
      <button class="search-button" @click="searchKeyword">搜索</button>
    </div>
    <div class="adv" :style="'background-image: url('+require('../../assets/images/5c7356b1b5.png')+')'">
    </div>
    <div class="type-class">
      <img src="../../assets/images/tb_x.png">
      <div class="right">
        <a>默认列表</a>
        <a>></a>
      </div>
    </div>
    <div class="items">
      <div class="item" @click="goDetail(item)" v-for="(item, index) in rows">
        <div class="img" :style="'background-image: url('+item.pic1+')'"></div>
        <div class="title">{{item.name}}</div>
        <div class="bottom">
          <a>￥{{item.price}}</a>
          <img src="../../assets/images/gwct_x.png">
        </div>
      </div>
    </div>

    <div class="blank" style="width: 100%;height: 50px"></div>
    <BottomBar :item="1"></BottomBar>

  </div>

</template>

<script>
  import Swiper from "swiper";
  import BottomBar from "../../components/BottomBar";
  import {dataPost} from "../../../plugins/axiosFn";

  export default {
    name: "Home",
    components: {
      BottomBar: BottomBar
    },
    data() {
      return {
        keyword:'',
        top: [
          {
            pic1: require('../../assets/images/5c1478d532.jpg')
          },
          {
            pic1: require('../../assets/images/5c14791447.jpg')
          },
          {
            pic1: require('../../assets/images/5c6e6d31e4.jpg')
          }
        ],
        rows: [],
        info:{}
      }
    },
    mounted() {
      new Swiper('.swiper-container', {
        autoplay: true,
        loop: false,
        pagination: {
          el: '.swiper-pagination',
        },
        scrollbar: {
          el: '.swiper-scrollbar',
        },
      })
      this.getList()
      console.log(this.$route.params.id)
      let info = JSON.parse(localStorage.getItem('info'))
      this.info = info
      this.getInfo()
      let shopper = JSON.parse(localStorage.getItem('shopper'))
      console.log(shopper,'shopper')


      if (!shopper) {
        console.log('!shopper')
        localStorage.removeItem('shopper')
        dataPost(this.GLOBALDATA.serverUrl+'/home/user/shopperInfo', {
          membership_id: info.referrer_id
        }, (response, all) => {
          console.log(response.data)
          localStorage.setItem('shopper',JSON.stringify(response.data))
        });
      }
    },
    methods: {
      getInfo() {
        dataPost(this.GLOBALDATA.serverUrl+'/home/user/info', {
        }, (response, all) => {
          localStorage.setItem('info',JSON.stringify(response.data))
          this.info = response.data
          this.info.avatar = this.GLOBALDATA.absoluteUrl+'/' + response.data.avatar
        })
      },
      searchKeyword(){
        if (this.keyword == '') {
          this.$Modal.warning({
            title: '注意',
            content: '请先输入关键词!',
          });
        } else {
          this.$router.push({
            path: '/goodsList',
            query: {
              keyword: this.keyword
            }
          })
        }
      },
      goDetail(e) {
        console.log(e)
        this.$router.push({path: '/commodity?id=' + e.id})
      },
      getList() {
        dataPost(this.GLOBALDATA.serverUrl+'/home/goods/homeList', {
          page_num: 1,
          page_size: 10
        }, (response, all) => {
          console.log(response.data)
          this.top = response.data.top
          this.rows = response.data.rows
        });
      }
    }
  }
</script>

<style scoped lang="less">
  .content::-webkit-scrollbar {
    display: none;
  }

  .content {
    position: fixed;
    width: 100%;
    height: 100%;
    overflow: scroll;
    a {
      color: black;
    }
    .swiper-container {
      width: 100%;
      height: 300px;
      .swiper-slide {
        background-size: cover;
        background-repeat: no-repeat;
        background-position: center;
      }
    }
    .search-text {
      width: 100%;
      background-color: #ff6633;
      padding: 10px;
      box-sizing: border-box;
      display: flex;
      flex-direction: row;
      input {
        border: none;
        height: 30px;
        padding-left: 10px;
        padding-right: 10px;
        width: 100%;
        font-size: 15px;
      }
      button {
        width: 50px;
        margin-left: 10px;
        border: none;

      }
    }
    .adv {
      width: 100%;
      height: 150px;
      background-size: cover;
      background-repeat: no-repeat;
      background-position: center;
    }
    .type-class {
      padding: 5px;
      padding-top: 10px;
      box-sizing: border-box;
      width: 100%;
      min-height: 10px;
      border-bottom: 1px solid #ff6735;
      display: flex;
      flex-direction: row;
      img {
        width: 25px;
        height: 25px;
      }
      .right {
        width: 100%;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        padding-left: 10px;
        padding-right: 10px;
      }
    }
    .items {
      width: 100%;
      padding: 10px;
      box-sizing: border-box;
      display: flex;
      flex-direction: row;
      flex-wrap: wrap;
      .item {
        width: 50%;
        padding: 0 5px;
        padding-bottom: 10px;
        box-sizing: border-box;
        display: flex;
        justify-content: space-between;
        flex-direction: column;
        .img {
          width: 100%;
          height: 150px;
          background-size: cover;
          background-repeat: no-repeat;
          background-position: center;
        }
        .title {
          font-size: 15px;
          overflow:hidden;
          text-overflow:ellipsis;
          display:-webkit-box;
          /*! autoprefixer: off */
          -webkit-box-orient: vertical;
          /* autoprefixer: on */
          -webkit-line-clamp:2;
        }
        .bottom {
          display: flex;
          flex-direction: row;
          justify-content: space-between;
          padding-top: 5px;
          a {
            color: red;
            font-size: 17px;
          }
          img {
            width: 20px;
            height: 20px;
          }
        }
      }
    }
  }
</style>
