<template>
  <view class="uni-container">
    <view class="pf-title pf-titleTwo">
      <img src="@/static/images/profession/pf-back.svg" @click="backpf">
      <text>发放支付</text>
    </view>
    <view class="pf-content">
      <view class="contract-ul">
        <view class="contract-li">
          <view>借款人</view>
          <view>{{paymentList.customerName}}</view>
        </view>
        <view class="contract-li">
          <view>身份证号</view>
          <view>{{paymentList.certId}}</view>
        </view>
        <view class="contract-li">
          <view>业务品种</view>
          <view>{{paymentList.businessType2}}</view>
        </view>
        <view class="contract-li" v-if="applyPhase == '01'">
          <view>批复金额（元）</view>
          <view>{{paymentList.businessSum2}}</view>
        </view>
      </view>
      <!--tab页签内容-->
      <view class="tab-ul" v-if="applyPhase == '05'">
        <view class="tab-li">
          <view class="tab-li-features">
            <view>《个人购房借款担保合同》</view>
            <view class="tab-color-orange">待发放</view>
          </view>
          <view class="tab-li-content">
            <view class="tab-top">
              <text>￥{{paymentList.businessSum2}}</text>
              <text></text>
            </view>
            <view class="tab-center">
              <view>
                <text>合同号</text>
                <text>{{paymentList.contractNo}}</text>
              </view>
              <view>
                <text>合同签署时间</text>
                <text>{{paymentList.occurDate}}</text>
              </view>
            </view>
            <view class="tab-bottom">
              <text @click="addLendApplySuccess">发起放款申请</text>
            </view>
          </view>
        </view>
      </view>
      <view class="contract-button" v-if="applyPhase == '01'">
        <button type="primary" @click="updatePayment">提交</button>
      </view>
    </view>
  </view>
</template>

<script>
  import {mapGetters,mapActions} from 'vuex';
  import filter from '@/utils/filters';
  export default {
    components: {},
    data() {
      return {
        applyPhase: '05',
        userID: '',
        orgId: '',
        orderNoVal: '',
        contractNoVal: '',
      };
    },
    watch:{},
    onLoad(options) {
      this.applyPhase = options.applyPhase;
    },
    onReady() {},
    onShareAppMessage() {},
    computed:{
      ...mapGetters(['paymentList','userInfor'])
    },
    created(){
      console.log(this.paymentList)
      this.userID = this.userInfor.loginCode;
      this.orgId =  this.userInfor.orgId;

      this.orderNoVal = this.paymentList.orderNo;
      this.contractNoVal = this.paymentList.contractNo;
    },
    methods: {
      ...mapActions(["businessNumCommit"]),
      //放款申请
      addLendApplySuccess(){
        let data = {
          'orderNo': this.orderNoVal,  //订单编号
          'contractNo': this.contractNoVal,  //合同编号
          // 'contractNo': '2017111400000086',  //合同编号
          
        }
        this.interfaceRequest('/api/lend/addLendApplySuccess',data,"get",(res)=>{
          console.log(res)
          yu.showModal({
            title: '申请成功',
            content: '审批通过后将自动放款',
            showCancel: false,
            cancelText: "我知道了",
            success:(res)=> {
              let data = {
                "userID": this.userID, //客户经理编号
                "orgID": this.orgId, //客户经理所属机构编号
              };
              this.businessNumCommit(data);
              if (res.confirm) {
                console.log('用户点击确定');
                yu.navigateTo({
                  url: 'payment?applyPhase=01',
                });
              }
            }
          });
        },function(err){
          console.log(err)
        });
      },
      //提交
      updatePayment(){
        yu.navigateTo({
          url: 'paymentSubmit',
        });
      },
      //返回
      backpf(){
        yu.navigateBack();
      },
    }
  };
</script>

<style lang='scss'>
  @import '~@styles/uni-nvue.css';
  @import '@/static/css/professionwf.less';
  .uni-container{
    background-color: #FFFFFF;
    padding: 30rpx 0;
  }
</style>
