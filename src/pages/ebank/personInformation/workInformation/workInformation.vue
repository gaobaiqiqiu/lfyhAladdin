<template>
  <view class="uni-container">
    <view class="pf-title pf-titleThree">
      <img src="@/static/images/firstroom/backArrow.svg" @click="navigateBack">
      <text>{{identity}}信息</text>
      <text class="pf-text" @click="submit(false)">暂存</text>
    </view>
    <!--信息完善进度条-start-->
    <view class="household-progress">
      <img src="@/static/images/firstroom/shortProgress03.svg" />
      <view class="uni-flex uni-row">
        <view class="flex-item active">户籍信息</view>
        <view class="flex-item active">基本信息</view>
        <view class="flex-item active">工作信息</view>
      </view>
    </view>
    <!--信息完善进度条-end-->
    <view class="pf-content work-content">
      <!--个人信息-start-->
      <view class="contract-ul marginT25">
        <view class="contract-li-tips">
          <view class="contract-li">
            <view>单位名称</view>
            <view>
              <input class="uni-input" placeholder="请输入" type="text" v-model.trim="personInfor.companyName" @focus="verifyCompanyName.isTipShow=false" @blur="checkCompanyName(personInfor.companyName)" />
              <img v-if="personInfor.companyName!=''" src="@/static/images/firstroom/cleanUp.svg" @click="personInfor.companyName=''" />
            </view>
          </view>
          <view v-if="verifyCompanyName.isTipShow" class="contract-tips">{{verifyCompanyName.tipText}}</view>
        </view>
        <view class="contract-li-tips">
          <view class="contract-li">
            <view>单位地址</view>
            <view>
              <input class="uni-input" placeholder="请输入" type="text" v-model.trim="personInfor.companyAddress" @focus="verifyCompanyAddress.isTipShow=false" @blur="checkCompanyAddress(personInfor.companyAddress)" />
              <img v-if="personInfor.companyAddress!=''" src="@/static/images/firstroom/cleanUp.svg" @click="personInfor.companyAddress=''" />
            </view>
          </view>
          <view v-if="verifyCompanyAddress.isTipShow" class="contract-tips">{{verifyCompanyAddress.tipText}}</view>
        </view>
        <view class="contract-li-tips">
          <view class="contract-li">
            <view>单位电话</view>
            <view>
              <input class="uni-input" placeholder="请输入" type="number" v-model.trim="personInfor.telephone" @focus="verifyTelephone.isTipShow=false" @blur="checkTelephone(personInfor.telephone)" />
              <img v-if="personInfor.telephone!=''" src="@/static/images/firstroom/cleanUp.svg" @click="personInfor.telephone=''" />
            </view>
          </view>
          <view v-if="verifyTelephone.isTipShow" class="contract-tips">{{verifyTelephone.tipText}}</view>
        </view>
        <view class="contract-li">
          <view>单位性质</view>
          <view>
            <picker @change="workNatureChange" :value="workNatureIdx" :range-key="'value'" :range="workNatureList">
              {{workNatureList[workNatureIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
        <view class="contract-li">
          <view>单位所属行业</view>
          <view>
            <picker @change="unitKindChange" :value="unitKindIdx" :range-key="'value'" :range="unitKindList">
              {{unitKindList[unitKindIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
        <view class="contract-li">
          <view>职务</view>
          <view>
            <picker @change="headShipChange" :value="headShipIdx" :range-key="'value'" :range="headShipList">
              {{headShipList[headShipIdx].value}}
            </picker>
            <img src="@/static/images/firstroom/formChooseArrow.svg" />
          </view>
        </view>
      </view>
      <!--个人信息-end-->
      <view class="contract-button">
        <button type="primary" @click="submit(true)">提交</button>
      </view>
    </view>
  </view>
</template>
<script>
  import pageHead from '@/components/page-head.vue';
  import { mapGetters,mapActions } from 'vuex'

  export default {
    data() {
      return {
        identity: "",
        personInfor:{
          companyName:"",
          companyAddress:'',
          telephone:''
        },
        verifyCompanyName: {
          isTipShow:false,
          tipText: '单位名称不能为空',
        },
        verifyCompanyAddress: {
          isTipShow:false,
          tipText: '单位地址不能为空',
        },
        verifyTelephone: {
          isTipShow:false,
          tipText: '单位电话不能为空',
        },
        unitKindList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        unitKindIdx: 0,
        workNatureList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        workNatureIdx: 0,
        headShipList: [
          {
            key: 0,
            value: '请选择'
          }
        ],
        headShipIdx: 0,
        orderNoVal: '',  //订单编号
        applyNoVal: '',  //申请编号
        isJump: true,
        preventResubmit: true,  //防重复提交
      }
    },
    computed: {
      ...mapGetters([
          'approvalIngList',
          'userInfor'
      ])
    },
    onLoad(option) {
      this.orderNoVal = this.approvalIngList.orderNo;
      this.applyNoVal = this.approvalIngList.serialNo;
      this.identity = option.identity;
      this.getUnitKindList();
      this.getWorkNatureList();
      this.getHeadShipList();
      if(localStorage.getItem('personalInformation')){
        this.getPersonalData();
      }
    },
    onShow() {},
    onUnload() {},
    methods: {
      ...mapActions(['queryApplyInfoCommit']),
      //返回上一页
      navigateBack(){
        // yu.showModal({
        //   title: '确定返回吗？',
        //   content: '数据还未提交，点确定后编辑过的内容将不保存！',
        //   success: function (res) {
        //     if (res.confirm) {
        //       uni.navigateBack();
        //     } else if (res.cancel) {
        //       console.log('用户点击取消');
        //     }
        //   }
        // }); 
        this.queryApplyInfoCommit({
          'orderNo': this.orderNoVal, 
          'applyNo': this.applyNoVal,
          'routerTrue': true,
          'routerTo': 'personInformation/personInformation',
          'routerJumpWay': 'pageJump'
        }); //重新调'申请信息查询'接口
      },
      //下拉列表
      unitKindChange(e){
        this.unitKindIdx = e.target.value
      },
      workNatureChange(e){
        this.workNatureIdx = e.target.value
      },
      headShipChange(e){
        this.headShipIdx = e.target.value
      },
      // 获取单位所属行业列表
      getUnitKindList(){
        let pdata={dictionaryName:"unitKind"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
              this.unitKindList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 获取单位性质列表
      getWorkNatureList(){
        let pdata={dictionaryName:"workNature"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
              this.workNatureList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 获取职务列表
      getHeadShipList(){
        let pdata={dictionaryName:"headShip"};
        this.queryDictionaryList(pdata,(res)=>{
          let resData=res.data.data;
          for(let key in resData){
              this.headShipList.push({
                key:key,
                value:resData[key]
              });
          }
        });
      },
      // 提交
      submit(isJump){
        if(!this.preventResubmit){
          yu.showModal({
            content: '操作进行中，请稍等',
            showCancel: false,
            cancelText: "确定",
            success: function (res) {
              if (res.confirm) {
                console.log('用户点击确定');
              }
            }
          });
          return;
        }
        this.preventResubmit = false;
        this.isJump = isJump;
        let personalInformation=JSON.parse(localStorage.getItem('personalInformation'));
        console.log(personalInformation)
        let data={
          userId: this.userInfor.loginCode,
          certType: personalInformation.certType,
          certId: personalInformation.certId,
          customerName: personalInformation.customerName,
          workCorp: this.personInfor.companyName,
          workAdd: this.personInfor.companyAddress,
          workTel: this.personInfor.telephone,
          workNature: this.workNatureList[this.workNatureIdx].key,
          unitKind: this.unitKindList[this.unitKindIdx].key,
          headShip: this.headShipList[this.headShipIdx].key,
          scene: 'cstScene',
          applyNo: this.applyNoVal,
          listName: 'workInfo'
        }
       // 3.7接口 个人信息更新
        let posturl="/api/credit/customerInfoUpdate";
        yu.showLoading();
        this.interfaceRequest(posturl,data,"post",(res)=>{
          this.preventResubmit = true;
          yu.hideLoading();
          let resArr=res.data.data;
          if(resArr.returnCode=='Success'){
            if(this.isJump){
              localStorage.removeItem('personalInformation');
              this.queryApplyInfoCommit({
                'orderNo': this.orderNoVal, 
                'applyNo': this.applyNoVal,
                'routerTrue': true,
                'routerTo': 'personInformation/personInformation',
                'routerJumpWay': 'pageJump'
              }); //重新调'申请信息查询'接口
            }else{
              yu.showToast({
                title: '暂存成功！',
                image: './static/images/perfectInformation/success.svg',
                duration: 2000
              });
            }
          }else{
            yu.showToast({
              title: resArr.returnDesc,
              icon: 'none',
              duration: 3000
            });
          }
        },(err)=>{
          this.preventResubmit = true;
          yu.hideLoading();
          yu.showToast({
            title: '3.7个人信息出现问题，请联系管理员',
            icon: 'none',
            duration: 3000
          });
        });
      },
      // 3.2接口 获取个人信息
      getPersonalData(){
        let personalInformation=JSON.parse(localStorage.getItem('personalInformation'));
          let data={
            certType: personalInformation.certType,
            certId: personalInformation.certId,
            customerName: personalInformation.customerName,
            scene: 'cstScene',
            applyNo: this.applyNoVal
          }
          let posturl="/api/front/perCustomerInfo";
          yu.showLoading();
          this.interfaceRequest(posturl,data,"post",(res)=>{
            yu.hideLoading();
            let resData=res.data.data;
            if(resData.returnCode=='Success'){
              this.personInfor.companyName=resData.workCorp;
              this.personInfor.companyAddress=resData.workAdd;
              this.personInfor.telephone=resData.workTel;
              this.workNatureIdx=this.selectInfo(this.workNatureList,resData.workNature);
              this.unitKindIdx=this.selectInfo(this.unitKindList,resData.unitKind);
              this.headShipIdx=this.selectInfo(this.headShipList,resData.headShip);
            }else{
              yu.showToast({
                title: resData.returnDesc,
                icon: 'none',
                duration: 3000
              });
            }
          },(err)=>{
            yu.hideLoading();
            yu.showToast({
              title: '3.2个人信息出现问题，请联系管理员',
              icon: 'none',
              duration: 3000
            });
          });
      },
      // 下拉框回显
      selectInfo(arr,parmName){
        if(parmName){
          let selIdx=0;
          arr.forEach((item, index) => {
            if(item.key==parmName){
              selIdx=index
            }
          });
          return selIdx
        }else{
          return 0
        }
      }
    },
    mounted(){
        
    }
  };


  
</script>

<style lang='scss' scoped>
  @import '@/static/css/professionwf.less';
  .uni-container{
    background-color: #f6f8f9;
    padding: 30rpx 0;
    .work-content{
      padding-top: 270rpx;
    
    }
  }
  .marginT25{
    margin-top: 25rpx;
  }
</style>
