<template>
  <div class="content">
    <div class="top">
      申请分销商
    </div>
    <div class="detail">
      <div class="form">
        <div class="clarify">
          申请成为分销商需要满足以下条件：<br>
          <a>{{regulationDetail.require_type|regulationF}}</a>
          <a v-if="regulationDetail.require_type==3">￥{{regulationDetail.require_expense}}</a>

          <!--1、有过消费记录<br>-->
          <!--2、消费额达到 ￥200<br>-->
          <!--3、直接购买分销资格<br>-->
        </div>

        <div class="type" v-if="regulationDetail.require_type==3">
          <a>分销资格消费额:</a>
          <span>￥{{regulationDetail.require_expense}}</span>
        </div>
        <div class="type" v-if="regulationDetail.require_type==1">
          <a>分销资格价格:</a>
          <span>￥{{regulationDetail.require_price}}</span>
        </div>
        <div class="type" v-if="regulationDetail.require_type==3||regulationDetail.require_type==2">
          <a>您的消费额:</a>
          <span>￥{{info.expense}}</span>
        </div>
        <div class="type" v-if="regulationDetail.require_type==1">
          <a>您的余额:</a>
          <span>￥{{info.balance}}</span>
        </div>
        <div class="type" style="background-color: #f8f8f8;border: none;"></div>
        <div class="type gray">
          <a>推荐人昵称:</a>
          <span>{{info.referrer}}</span>
        </div>
        <div class="type gray">
          <a>推荐人会员ID:</a>
          <span>{{info.referrer_id}}</span>
        </div>
      </div>
      <div class="agreement" v-if="regulationDetail.display_agreement">
        <a @click="goAgreement">查看购买协议内容</a>
      </div>
      <div class="submit">
        <Button type="success" size="large" :disabled="info.status!=0"
                style="background-color: #169bd5;border-color: #169bd5" @click="applySubmit" long>{{tip}}
        </Button>
      </div>
    </div>



    <div class="blank" style="width: 100%;height: 50px"></div>
    <BottomBar :item="3"></BottomBar>
    <GoBack></GoBack>
  </div>
</template>

<script>
  import BottomBar from "../../components/BottomBar";
  import GoBack from "../../components/GoBack";
  import {dataPost} from "../../../plugins/axiosFn";

  export default {
    name: "Apply",
    components: {
      BottomBar: BottomBar,
      GoBack: GoBack,
    },
    data() {
      return {
        regulationDetail: {
          require_type: 1
        },
        info: {},
        tip: '申请成销商'
      }
    },
    methods: {
      goAgreement(){
        this.$Modal.info({
          title: this.regulationDetail.agreement_title,
          content: this.regulationDetail.agreement_content
        });
      },
      getRegulation() {
        dataPost(this.GLOBALDATA.serverUrl+'/home/regulation/detail', {}, (response, all) => {
          console.log(response.data)
          this.regulationDetail = response.data
        })
      },
      getInfo() {
        dataPost(this.GLOBALDATA.serverUrl+'/home/membership/getMembership', {
          id: this.info.id
        }, (response, all) => {
          console.log(response.data)
          this.info = response.data
          this.judgeStatus()
        });
      },
      applySubmit() {
        if (this.regulationDetail.require_type == 1) {
          this.$Modal.confirm({
            title: '提示',
            content: '确定进行分销资格购买吗？',
            onOk: () => {
              if (this.info.balance < this.regulationDetail.require_price) {
                setTimeout(() => {
                  this.$Modal.warning({
                    title: '提示',
                    content: '您的余额不足，请及时充值！',
                  })
                }, 500)

              } else {
                //进行购买
                dataPost(this.GLOBALDATA.serverUrl+'/home/membership/updateStatus', {
                  id: this.info.id,
                  status: 1,
                  require_type: this.regulationDetail.require_type,
                  require_expense: this.regulationDetail.require_expense,
                  require_price: this.regulationDetail.require_price,
                }, (response, all) => {
                  console.log(response.data, 555)
                  setTimeout(() => {
                    this.$Modal.success({
                      title: '提示',
                      content: '购买成功！请等待审核结果',
                      onOk: () => {
                        this.getInfo()
                      }
                    })
                  }, 500)
                });
              }

            }
          });

        } else if (this.regulationDetail.require_type == 2) {
          this.$Modal.confirm({
            title: '提示',
            content: '确定申请分销商吗？',
            onOk: () => {
              if (this.info.expense > 0) {
                //申请
                dataPost(this.GLOBALDATA.serverUrl+'/home/membership/updateStatus', {
                  id: this.info.id,
                  status: 1,
                  require_type: this.regulationDetail.require_type,
                  require_expense: this.regulationDetail.require_expense,
                  require_price: this.regulationDetail.require_price,
                }, (response, all) => {
                  console.log(response.data, 555)
                  setTimeout(() => {
                    this.$Modal.success({
                      title: '提示',
                      content: '申请成功！请等待审核结果',
                      onOk: () => {
                        this.getInfo()
                      }
                    })
                  }, 500)
                });
              } else {
                setTimeout(() => {
                  this.$Modal.warning({
                    title: '提示',
                    content: '您暂未有消费记录！',
                  })
                }, 500)
              }
            }
          })
        } else if (this.regulationDetail.require_type == 3) {
          this.$Modal.confirm({
            title: '提示',
            content: '确定申请分销商吗？',
            onOk: () => {
              if (this.info.expense > this.regulationDetail.require_expense) {
                //申请
                dataPost(this.GLOBALDATA.serverUrl+'/home/membership/updateStatus', {
                  id: this.info.id,
                  status: 1,
                  require_type: this.regulationDetail.require_type,
                  require_expense: this.regulationDetail.require_expense,
                  require_price: this.regulationDetail.require_price,
                }, (response, all) => {
                  console.log(response.data, 555)
                  setTimeout(() => {
                    this.$Modal.success({
                      title: '提示',
                      content: '申请成功！请等待审核结果',
                      onOk: () => {
                        this.getInfo()
                      }
                    })
                  }, 500)
                });
              } else {
                setTimeout(() => {
                  this.$Modal.warning({
                    title: '提示',
                    content: '您暂未达到指定消费额！',
                  })
                }, 500)
              }
            }
          })
        }


        // this.$router.push({path: '/'})
      },
      judgeStatus() {
        switch (this.info.status) {
          case 0:
            this.tip = "申请成为分销商"
            break;
          case 1:
            this.tip = "审核中"
            break;
          case 2:
            this.tip = "申请分销商成功"
            break;
          case 3:
            this.tip = "申请已被拒绝"
            break;
          case 4:
            this.tip = "分销功能已被禁用"
            break;
        }
      }
    },
    mounted() {
      this.info = JSON.parse(localStorage.getItem('info'))
      this.getInfo()
      this.getRegulation()

    },
    filters: {
      regulationF(val) {
        switch (val) {
          case 1:
            return '直接购买分销资格'
          case 2:
            return '购买任意商品'
          case 3:
            return '消费额达到'
        }
      },
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
    .detail {
      font-size: 15px;
      width: 100%;
      box-sizing: border-box;
      padding: 10px;
      .tip {
        width: 100%;
        span {
          color: red;
        }
      }
      .form {
        margin-top: 10px;
        width: 100%;
        background-color: white;
        .clarify {
          a {
            padding: 5px 0;
            font-weight: bold;
            display: block;
          }
        }
        div {
          box-sizing: border-box;
          padding: 5px 10px;
          border-bottom: 1px solid gainsboro;
        }
        .type {
          display: flex;
          flex-direction: row;
          flex-wrap: nowrap;
          align-items: center;
          a {
            width: 130px;
            display: block;
          }
          span {
            color: red;
          }
        }

        .gray {
          span {
            color: gray;
          }
        }
      }
      .agreement{
        margin-top: 5px;
        margin-left: 5px;
        font-size: 14px;
      }
      .submit {
        width: 100%;
        display: flex;
        justify-content: center;
        padding: 20px 0;
        .button {
          width: 90%;
          background-color: #169bd5;
          color: white;
          display: flex;
          justify-content: center;
          align-items: center;
          border-radius: 5px;
          height: 40px;
        }
      }
    }
  }
</style>
