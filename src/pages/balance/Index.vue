<template>
  <div class="content">
    <div class="top">
      余额
    </div>
    <div class="balance">
      <a>我的余额</a>
      <span>￥{{info.balance}}</span>
    </div>


    <div class="items">
      <div class="item" @click="goHistory">
        <img src="../../assets/images/ic-list2.png">
        <a>充值记录</a>
      </div>
      <!--<div class="item">-->
      <!--<img src="../../assets/images/money2.png">-->
      <!--<a>余额流水</a>-->
      <!--</div>-->
    </div>
    <div class="charge">
      <div class="button" @click="chargeBalance">充值</div>
    </div>
    <div class="blank" style="width: 100%;height: 50px"></div>
    <BottomBar :item="5"></BottomBar>
    <GoBack></GoBack>
  </div>
</template>

<script>
  import BottomBar from "../../components/BottomBar";
  import GoBack from "../../components/GoBack";
  import {Message} from "element-ui";
  import {dataPost} from "../../../plugins/axiosFn";

  export default {
    name: "Balance",
    components: {
      BottomBar: BottomBar,
      GoBack: GoBack,
    },
    data() {
      return {
        info: {},
        balance: ''
      }
    },
    methods: {
      goHistory() {
        this.$router.push({
          path: '/balanceHistory'
        })
      },
      chargeBalance() {
        this.balance = ''
        this.$Modal.confirm({
          title: '充值金额',
          render: (h) => {
            return h('Input', {
              props: {
                value: this.balance,
                type: 'number',
                autofocus: true,
                placeholder: '请输入充值金额'
              },
              on: {
                input: (val) => {
                  this.balance = val;
                  console.log(this.balance, 1)
                }
              }
            })
          },
          onOk: () => {
            if (this.balance != '') {
              dataPost(this.GLOBALDATA.serverUrl+'/home/charge/charge', {
                membership_id: this.info.membership_id,
                nickname: this.info.nickname,
                charge_account: this.balance,
              }, (response, all) => {
                console.log(response.data)
                Message({message: '充值成功！', type: 'success'});
                this.getInfo()
              });
            }

          }
        })
      },
      getInfo() {
        dataPost(this.GLOBALDATA.serverUrl+'/home/membership/getMembership', {
          id: this.info.id
        }, (response, all) => {
          console.log(response.data)
          this.info = response.data
        });
      }
    },
    mounted() {
      let info = JSON.parse(localStorage.getItem('info'))
      this.info = info
      this.getInfo()
    }
  }
</script>

<style scoped lang="less">
  .content {
    position: fixed;
    width: 100%;
    height: 100%;
    overflow: scroll;
    background-color: #f8f8f8;
    font-size: 15px;
    color: #262626;
    a {
      color: #262626;
    }
    .top {
      width: 100%;
      background-color: white;
      border-bottom: 1px solid gainsboro;
      text-align: center;
      padding: 10px 0;
    }
    .balance {
      width: 100%;
      display: flex;
      flex-direction: column;
      padding: 10px;
      box-sizing: border-box;
      span {
        color: red;
        font-size: 20px;
      }
      a {
        font-weight: bold;
        font-size: 17px;
      }
    }
    .charge {
      margin-top: 20px;
      width: 100%;
      display: flex;
      justify-content: center;
      .button {
        width: 90%;
        border-radius: 5px;
        background-color: #169bd5;
        padding: 10px;
        text-align: center;
        color: white;
      }
    }
    .items {
      width: 100%;
      margin-top: 10px;
      .item {
        width: 100%;
        background-color: white;
        border: 1px solid gainsboro;
        display: flex;
        flex-direction: row;
        align-items: center;
        padding: 5px 10px;
        box-sizing: border-box;
        img {
          width: 27px;
          height: 27px;
        }
        a {
          margin-left: 5px;
        }
      }
    }
  }
</style>
