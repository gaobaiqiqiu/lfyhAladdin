<template>
  <view class="uni-container">
    <view class="pf-title pf-titleTwo">
      <img src="@/static/images/profession/pf-back.svg" @click="backpf">
      <text>征信信息</text>
    </view>
    <view class="pf-content appro-content"> 
      <!--tab页签内容-->
      <view class="tab-ul">
        <view class="tab-li">
          <view class="tab-li-content">
            <view class="tab-center">
              <view>
                <text>申请编号</text>
                <text>{{dataObj.applyNo}}</text>
              </view>
              <view>
                <text>借款人姓名</text>
                <text>{{dataObj.customerName}}</text>
              </view>
              <view>
                <text>贷款申请金额（元）</text>
                <text>{{dataObj.businessSum}}</text>
              </view>
              <view>
                <text>申请期限（月）</text>
                <text>{{dataObj.termMonth}}</text>
              </view>
              <view>
                <text>收入负债比（%）</text>
                <text>{{dataObj.outInRatio}}</text>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>
    <view class="contract-button">
      <button type="primary" @click="toCreditSubmit">提交</button>
    </view>
  </view>
</template>

<script>
import { mapGetters} from "vuex";
export default {
    data: function() {
      return {
        orderNoVal: "", //订单编号
        applyNoVal: "", //申请编号,
        dataObj: {},
      }
    },
    computed: {
      ...mapGetters(["approvalIngList"])
    },
    created() {
      this.orderNoVal = this.approvalIngList.orderNo;
      this.applyNoVal = this.approvalIngList.serialNo;
      this.applyriskintelligencecheck();
    },
    methods:{
      applyriskintelligencecheck(){
        let data = {
          'orderNo': this.orderNoVal,  //订单编号
          // 'applyNo': '2020122300000038',  //申请编号
          'applyNo': this.applyNoVal,  //申请编号
        }
        this.interfaceRequest('/api/lendriskintelligencecheck/applyriskintelligencecheck',data,"get",(res)=>{
          console.log(res)
          this.dataObj = res.data.data;
        },function(err){
          console.log(err)
        });
      },
      //返回
      backpf(){
        // yu.navigateBack();
        yu.redirectTo({ url: '/pages/ebank/perfectInformation/perfectInformation' })
      },
      toCreditSubmit(){
        yu.navigateTo({ url: 'creditSubmit' });
      }
    }
}
</script>
<style lang='scss'>
  @import '~@styles/uni-nvue.css';
  @import '@/static/css/professionwf.less';
.uni-container{
  background-color: #FFFFFF;
  padding: 30rpx 0;
  .appro-content{
    .tab-ul{
      padding: 40rpx 30rpx 24rpx;
      .tab-li{
        padding: 28rpx 0 28rpx 32rpx;
      }
    }
  }
}
</style>