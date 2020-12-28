<template>
  <view>
    <navigation-custom
      :config="config"
      :scrollTop="scrollTop"
      @customConduct="customConduct"
      :scrollMaxHeight="scrollMaxHeight"
    />
    <view :style="{ height: height, width: '100%' }"></view>
    <view v-if="tikuInfo.topic_three_file">
      <view class="tiku_info">
        <view class="img_box">
          <image
            :src="imgUrl + tikuInfo.topic_three_file"
            mode=""
            class="image"
          />
          <!-- <view class="text">共{{tikuInfo.num}}题</view> -->
        </view>

        <view class="info">
          <text class="title">{{ tikuInfo.topic_three_name }} </text>
          <view v-if="tikuInfo.t_status == 0" class="title-1">
            (免费题库)
          </view>
          <view v-else-if="tikuInfo.t_status == 1" class="title-1">
            (VIP免费题库)
          </view>
          <view v-else-if="tikuInfo.t_status == 2" class="title-1">
            (付费题库)
          </view>
          <view class="introduce">
            {{ tikuInfo.topic_three_intro }}
          </view>

          <view
            class="info-1"
            v-if="
              isVip == 0
                ? tikuInfo.isBuy == 0 && tikuInfo.t_status != 0
                : tikuInfo.isBuy == 0 && tikuInfo.t_status == 2
            "
          >
            <view class="info-1-1">
              {{ tikuInfo.price }}
            </view>
            <view class="info-1-2" @tap="open(tikuInfo.price)"> 购买 </view>
          </view>
        </view>
      </view>
      <!-- <view class="tiku_type">
			    <block v-if="tikuInfo.t_status == 0">
			        本题库为免费题库
			    </block>
			    <block v-else-if="tikuInfo.t_status == 1">
			        VIP免费题库
			    </block>
			    <block v-else-if="tikuInfo.t_status == 2">
			        付费题库
			    </block>
			</view> -->

      <block v-if="tikuInfo.t_status == 2">
        <block v-if="tikuInfo.isBuy == '1'">
          <block v-if="tikuInfo.didNum == 0">
            <view class="btn_box">
              <button class="dati_btn" @tap="goDati">开始答题</button>
            </view>
          </block>

          <block v-else>
            <block v-if="tikuInfo.didNum < tikuInfo.num">
              <view class="btn_box">
                <button class="dati_btn" @tap="gojiDati">继续答题</button>
                <button class="dati_btn" @tap="againDati">重新答题</button>
              </view>
            </block>

            <block v-else-if="tikuInfo.didNum == tikuInfo.num">
              <view class="btn_box">
                <button class="dati_btn" @tap="againDati">重新答题</button>
              </view>
            </block>
          </block>
        </block>
        <!--  <block v-else-if="tikuInfo.isBuy == '0'">
			        <block v-if="isVip == 0">  
			            <view class="btn_box">
			                <button class="red_btn" @tap="buyFun(tikuInfo.price,tikuInfo.topic_three_id)">购买：￥{{tikuInfo.price}}</button>
			            </view>
						
						<view class="btn_text">
						
						</view>
			        </block>  
			        <block v-if="isVip == 1">  
			            <view class="btn_box">
			                <button class="red_btn" @tap="buyFun(vipPrice(tikuInfo.price),tikuInfo.topic_three_id)">购买：￥{{vipPrice(tikuInfo.price)}}</button>
			            </view>
						<view class="btn_text">
							
						</view>
			        </block>  
			        
			    </block>  -->
      </block>

      <block v-else-if="tikuInfo.t_status == 1">
        <!-- <block v-if="isVip == 0">
			        <view class="btn_box">
			            <button class="red_btn" @tap="buyVip">购买VIP</button>
			        </view>
			    </block> -->

        <block v-if="isVip == 1">
          <view class="btn_box">
            <block v-if="tikuInfo.didNum == 0">
              <view class="btn_box">
                <button class="dati_btn" @tap="goDati">开始答题</button>
              </view>
            </block>

            <block v-else>
              <block v-if="tikuInfo.didNum < tikuInfo.num">
                <view class="btn_box">
                  <button class="dati_btn" @tap="gojiDati">继续答题</button>
                  <button class="dati_btn" @tap="againDati">重新答题</button>
                </view>
              </block>

              <block v-else-if="tikuInfo.didNum == tikuInfo.num">
                <view class="btn_box">
                  <button class="dati_btn" @tap="againDati">重新答题</button>
                </view>
              </block>
            </block>
          </view>
        </block>
      </block>

      <block v-else-if="tikuInfo.t_status == 0">
        <block v-if="tikuInfo.didNum == 0">
          <view class="btn_box">
            <button class="dati_btn" @tap="goDati">开始答题</button>
          </view>
        </block>

        <block v-else>
          <block v-if="tikuInfo.didNum < tikuInfo.num">
            <view class="btn_box">
              <button class="dati_btn" @tap="gojiDati">继续答题</button>
              <button class="dati_btn" @tap="againDati">重新答题</button>
            </view>
          </block>
          <block v-else-if="tikuInfo.didNum == tikuInfo.num">
            <view class="btn_box">
              <button class="dati_btn" @tap="againDati">重新答题</button>
            </view>
          </block>
        </block>
      </block>
      <block v-for="(item, index) in chapter_list" :key="index">
        <view class="text" @tap="goanswer(item.chapter)">
          <view class="ch">
            <view class="text-1">
              <view class="text-1-1"> 章节 </view>
              <view class="text-1-2">
                {{ item.chapter }}
              </view>
            </view>
            <view class="text-2">
              {{ item.chapter_name }}
            </view>
          </view>
          <view class="text-t">
            <image
              src="../../static/images/mianfei.png"
              mode=""
              class="text-i"
              v-if="item.is_pay == 0"
            ></image>

            <image
              src="../../static/images/fufei.png"
              mode=""
              class="text-i"
              v-if="item.is_pay == 1"
            ></image>
          </view>
        </view>
      </block>
    </view>

    <uni-popup ref="popup" type="bottom">
      <view class="popup">
        <view class="popup-t">
          <view class="popup-t-1">订单详情</view>
          <view class="popup-t-2" @tap="close">x</view>
        </view>

        <view class="popup-c">
          <view class="popup-c-1">
            <view class="popup-c-1-1">原价</view>
            <view class="popup-c-1-1-2">{{ tikuInfo.price }}</view>
          </view>

          <view class="popup-c-1">
            <view class="popup-c-1-1" v-if="isVip == 1">
              <text class="popup-c-l">vip折扣</text>
              <text
                class="popup-c-r"
                v-if="varied_content ? 'varied_content' : ''"
                >{{ varied_content }}折</text
              >
            </view>
            <view class="popup-c-1-1-2" v-if="isVip == 1">{{
              vipPrice(tikuInfo.price, varied_content)
            }}</view>
            <!-- <view class="popup-c-1-1-2" v-if="isVip==0">{{tikuInfo.price}}</view> -->
          </view>

          <view class="popup-c-1">
            <view class="popup-c-1-1">
              <text class="popup-c-l">拼团折扣</text>
              <text class="popup-c-r">(小星星数: {{ xxnumber }}个)</text>
            </view>
            <view class="popup-c-1-1-2" v-if="xxnumber != 0">
              <picker @change="bindPickerChange" :value="index" :range="array">
                <view class="uni-input">{{ array[index] }}</view>
              </picker>
            </view>
          </view>
        </view>

        <view class="popup-b">
          <view class="popup-b-1">
            <text class="popup-b-l">合计：</text>
            <text class="popup-b-c">￥{{ countp }}</text>
            <text class="popup-b-r">(已优惠{{ yh_p }}元)</text>
          </view>
          <view class="popup-b-2" @tap="buyFun(countp, tikuInfo.topic_three_id)"
            >确认支付</view
          >
        </view>
      </view>
    </uni-popup>
  </view>
</template>

<script>
import navigationCustom from "@/components/struggler-navigationCustom/navigation-custom";
import uniPopup from "@/components/uni-popup/uni-popup.vue";

export default {
  components: {
    uniPopup,
    navigationCustom,
  },
  data() {
    return {
      array: ["选择", "使用", "不使用"],
      chapter_list: [],
      index: 0,
      isVip: "",
      vipCode: "",
      xxcode: "",
      xxnumber: "",
      xxid: "",
      vipP: "",
      countp: "",
      yh_p: "",
      c_id: "",

      topic_three_id: "",
      topic_one_id: "",
      tikuInfo: {},
      imgUrl: this.$imgUrl,
      // isVip: '',
      varied_content: "", //折扣
      config: {
        title: "题库信息", //title
        bgcolor: "white", //背景颜色
        fontcolor: "black", //文字颜色，默认白色
        type: 1, //type 1，3胶囊 2，4无胶囊模式
        transparent: true, //是否背景透明 默认白色
        linear: true, //是为开启下滑渐变
        share: false, //是否将主页按钮显示为分享按钮
        back: "../../static/icon/back_.png",
        home: "../../static/icon/home_.png",
        // menuIcon:"../static/icon/back_.png", 当type为3或者4的时候左边的icon文件位置，注意位置与当前页面不一样
        // menuText:"返回", 当type为3或4的时候icon右边的文字
      },
      scrollTop: 0, // 当linear为true的时候需要通过onpagescroll传递参数
      scrollMaxHeight: 200, //滑
      height: this.$store.state.navheight,
    };
  },
  onLoad(options) {
    this.topic_three_id = options.id;
    this.topic_one_id = options.topic_one_id;
    console.log(options, 999999);
    this.selectTopicThree();
    this.selectVaried();
    this.getChapter();
    this.selectxingxing();

    // this.isVip = uni.getStorageSync('userInfo').is_vip;
    // this.isVip = 0;
    // this.isVip = 1;
  },
  computed: {
    vipPrice() {
      let that = this;
      return function (price) {
        return ((price * that.varied_content) / 10).toFixed(2);
      };
    },
  },
  methods: {
    bindPickerChange: function (e) {
      console.log("picker发送选择改变，携带值为", e.target.value);
      this.index = e.target.value;
      if (this.index == 2) {
        this.xxcode = 0;
      } else {
        this.xxcode = this.xxcode;
      }
    },
    open(price) {
      let aa = 1;
      let bb = 1;
      this.$refs.popup.open();
      if (this.isVip == 0) {
        aa = 1;
        if (this.xxcode != 0) {
          let bb = this.xxcode;
        } else {
          bb = 1;
        }

        console.log(aa, bb, 88);
      } else if (this.isVip == 1) {
        aa = this.varied_content / 10;
        if (this.xxcode != 0) {
          bb = this.xxcode;
        } else {
          bb = 1;
        }

        console.log(aa, bb, 99);
      }
      this.countp = (price * aa * bb).toFixed(2);
      this.yh_p = (price - this.countp).toFixed(2);
      console.log(this.countp, 88);
      console.log(price, aa, bb, 99);

      console.log(this.countp, this.yh_p);
    },
    getxingxing() {
      let data = {
        touch_id: this.xxid,
        // user_id:uni.getStorageSync('user_id'),
        // openId:uni.getStorageSync('openId')
      };
      this.$myRequest.get("hc/updateTouch.do", data).then((res) => {
        console.log(res, 999);
        if (res.code == 200) {
          // this.xxnumber = res.data
          // this.tikuInfo = res.data[0]
          // this.selectxingxing()
        }
      });
    },
    close() {
      this.$refs.popup.close();
    },
    async selectTopicThree() {
      let data = {
        topic_three_id: this.topic_three_id,
        topic_one_id: this.topic_one_id,
        // topic_one_id:
        user_id: uni.getStorageSync("user_id"),
        openId: uni.getStorageSync("openId"),
        // 不是vip
        // user_id:'7bec4be6073a450c8cba1ae28652ef00',
        // openId:'oU-0Y4wB0-UGKOpmqddu91iY0tUU' ,

        // // 是vip
        // user_id:'29d4b0bc2a68406184f1af16eaef0050',
        // openId:'oU-0Y41q8aLlm5WOzzrd60Q1EZHk' ,
      };
      const res = await this.$myRequest.get("hc/selectTopicThree.do", data);
      if (res.code == 200) {
        this.tikuInfo = res.data[0];
      }
    },
    async selectVaried() {
      const res = await this.$myRequest.get("hc/selectVaried.do", {});
      if (res.code == 200) {
        this.varied_content = res.data[4].varied_content;
      }
    },
    //获取章节
    async getChapter() {
      let data = {
        topic_three_id: this.topic_three_id,
        user_id: uni.getStorageSync("user_id"),
        openId: uni.getStorageSync("openId"),
        // 不是vip
        // user_id:'7bec4be6073a450c8cba1ae28652ef00',
        // openId:'oU-0Y4wB0-UGKOpmqddu91iY0tUU' ,

        // // 是vip
        // user_id:'29d4b0bc2a68406184f1af16eaef0050',
        // openId:'oU-0Y41q8aLlm5WOzzrd60Q1EZHk' ,
      };
      const res = await this.$myRequest.get("hc/getChapter.do", data);
      if (res.code == 200) {
        this.chapter_list = res.data;
        this.c_id = res.data[0].chapter;
        this.chapter_length = res.data.length;
        // this.c_id=res.data[0].chapter_id

        // this.tikuInfo = res.data[0]
      }
    },

    async selectxingxing() {
      let data = {
        openId: uni.getStorageSync("openId"),
        // openId:'oU-0Y4wB0-UGKOpmqddu91iY0tUU'
      };
      const res = await this.$myRequest.get("hc/selectUserStarDis.do", data);
      console.log(res, 999);
      if (res.code == 200) {
        this.xxcode = res.data.dis;
        this.xxnumber = res.data.count;
        this.xxid = res.data.touch_id;
        // this.vipCode = res.data[4].varied_content
      }
    },

    goDati() {
      let data = {
        topic_three_id: this.topic_three_id,
        user_id: uni.getStorageSync("user_id"),
        // user_id:"7bec4be6073a450c8cba1ae28652ef00",
        chapter: 1,
      };

      this.$myRequest.get("hc/getTopicChapter.do", data).then((res) => {
        if (res.code == 200) {
          uni.navigateTo({
            url: `../answerQuestions/answerQuestions?id=${this.topic_three_id}&c_id=${this.c_id}&chapter_length=${this.chapter_length}`,
          });
        } else {
          uni.showToast({
            title: res.msg,
            duration: 2000,
            icon: "none",
          });
        }
      });
    },

    gojiDati() {
      let data = {
        topic_three_id: this.topic_three_id,
        user_id: uni.getStorageSync("user_id"),
        // user_id:"7bec4be6073a450c8cba1ae28652ef00",
      };

      this.$myRequest.get("hc/getKeepOnTopic.do", data).then((res) => {
        if (res.code == 200) {
          let id = res.data.topic_id;
          let chapter = res.data.chapter;
          uni.navigateTo({
            url: `../answerQuestions/answerQuestions?id=${this.topic_three_id}&ids=${id}&chapter=${chapter}&c_id=${this.c_id}&chapter_length=${this.chapter_length}`,
          });
        } else {
          uni.showToast({
            title: res.msg,
            duration: 2000,
            icon: "none",
          });
        }
      });
    },

    goanswer(id) {
      // console.log('定价。。。。。')

      let data = {
        topic_three_id: this.topic_three_id,
        user_id: uni.getStorageSync("user_id"),
        // user_id:"7bec4be6073a450c8cba1ae28652ef00",
        chapter: id,
      };

      this.$myRequest.get("hc/getTopicChapter.do", data).then((res) => {
        if (res.code == 200) {
          uni.navigateTo({
            url: `../answerQuestions/answerQuestions?id=${this.topic_three_id}&chapter_id=${id}&chapter_length=${this.chapter_length}`,
          });
        } else {
          uni.showToast({
            title: res.msg,
            duration: 2000,
            icon: "none",
          });
        }
      });

      console.log(id, 9999);
      // return
    },
    againDati() {
      let data = {
        topic_three_id: this.topic_three_id,
        user_id: uni.getStorageSync("user_id"),
        // user_id:"7bec4be6073a450c8cba1ae28652ef00"
      };
      this.$myRequest.post("hc/deleteUserTopic.do", data).then((res) => {
        if (res.code == 200) {
          this.selectTopicThree();
          this.selectVaried();
          this.getChapter();
          this.selectxingxing();
          // uni.navigateTo({
          //     url: `../answerQuestions/answerQuestions?id=${this.topic_three_id}&chapter_length=${this.chapter_length}`
          // });
        }
      });
    },
    buyFun(price, id) {
      let that = this;
      let provider;
      uni.getProvider({
        service: "oauth",
        success: function (res) {
          provider = res.provider;
          console.log(res.provider);
        },
      });
      let data = {
        user_id: uni.getStorageSync("user_id"),
        // user_id:'7bec4be6073a450c8cba1ae28652ef00',
        openId: uni.getStorageSync("openId"),
        order_money: price,
        topic_three_id: id,
        t_type: 1,
        t_discount: this.varied_content,
      };
      this.$myRequest.post("OrderManager/suerOrder.do", data).then((result) => {
        if (result.code == 200) {
          uni.requestPayment({
            provider: provider,
            timeStamp: result.data.RESULTMAP.timeStamp,
            nonceStr: result.data.RESULTMAP.nonceStr,
            package: result.data.RESULTMAP.package,
            signType: "MD5",
            paySign: result.data.RESULTMAP.paySign,
            success: function (res) {
              console.log("success:" + JSON.stringify(res));
              that.selectTopicThree();
              that.getxingxing();
              this.close();
            },
            fail: function (err) {
              console.log("fail:" + JSON.stringify(err));
            },
          });
        }
      });
    },
    buyVip() {
      uni.switchTab({
        url: "../index/VIP/VIP",
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.popup {
  width: 100%;
  border-top-left-radius: 20rpx;
  border-top-right-radius: 20rpx;
  background-color: #fff;
  .popup-t {
    width: 100%;
    display: flex;
    padding: 38rpx 0;
    border-bottom: 1rpx solid #b5b5b5;
    .popup-t-1 {
      margin: 0 auto;
      font-family: PingFangSC-Medium;
      font-size: 32rpx;
      color: #333333;
      font-weight: 600;
      // margin-left: 63rpx;
    }
    .popup-t-2 {
      margin-right: 63rpx;
      color: #333333;
    }
  }
  .popup-c {
    padding: 30rpx 0 8rpx;
    width: 80%;
    margin: 0 auto;
    .popup-c-1 {
      display: flex;
      justify-content: space-between;
      .popup-c-1-1 {
        font-family: PingFangSC-Regular;
        padding-bottom: 25rpx;
        font-size: 32rpx;
        color: #333333;
        .popup-c-r {
          margin-left: 10rpx;
          color: #ff4141;
        }
      }
    }
  }
  .popup-b {
    width: 100%;
    display: flex;
    justify-content: space-between;
    // margin: 0 auto;
    padding: 25rpx 35rpx;
    box-shadow: 0rpx 0rpx 11rpx 2rpx rgba(0, 0, 0, 0.07);
    .popup-b-1 {
      margin-left: 34rpx;
      margin-top: 5rpx;
      .popup-b-l {
        font-size: 40rpx;
        color: #333333;
      }
      .popup-b-c {
        color: #ff4141;
      }
      .popup-b-r {
        font-size: 22rpx;
        color: #777777;
      }
    }

    .popup-b-2 {
      margin-right: 66rpx;
      width: 27%;
      height: 77rpx;
      background-color: #ffc741;
      border-radius: 10rpx;
      text-align: center;
      line-height: 77rpx;
      color: #fff;
      font-size: 32rpx;
    }
  }
}
.tiku_info {
  display: flex;
  // justify-content: space-between;
  padding: 50rpx 50rpx 0;
  box-sizing: border-box;
  border-bottom: 2rpx solid #ededed;
  .img_box {
    text-align: center;
    .image {
      width: 200rpx;
      height: 200rpx;
      background: red;
    }
  }

  .info {
    margin-left: 15rpx;
    .title {
      font-family: PingFangSC-Regular;
      font-size: 34rpx;
      font-weight: normal;
      font-stretch: normal;
      letter-spacing: 0rpx;
      color: #000000;
    }
    .title-1 {
      font-family: PingFangSC-Regular;
      font-size: 26rpx;
      font-weight: normal;
      font-stretch: normal;
      letter-spacing: 0rpx;
      color: #ffc741;
    }
    .introduce {
      font-family: PingFangSC-Regular;
      font-size: 28rpx;
      font-weight: normal;
      font-stretch: normal;
      letter-spacing: 0rpx;
      color: #000000;
      margin-top: 14rpx;
    }
    .info-1 {
      display: flex;
      justify-content: space-between;
      .info-1-1 {
        font-family: PingFangSC-Medium;
        font-size: 36rpx;
        color: #ff4141;
      }
      .info-1-2 {
        width: 30%;
        height: 43rpx;
        background-color: #ff4141;
        border-radius: 10rpx;
        text-align: center;
        line-height: 43rpx;
        color: #fff;
        font-size: 23rpx;
      }
    }
  }
}

.text {
  position: relative;
  width: 90%;
  margin: 0 auto;
  margin-bottom: 25rpx;
  margin-top: 32rpx;
  padding: 15rpx 0 15rpx 19rpx;
  box-shadow: 0rpx 0rpx 11rpx 2rpx rgba(0, 0, 0, 0.03);
  .ch {
    display: flex;
    .text-1 {
      width: 10%;
      height: 72rpx;
      background-color: #ffc741;
      padding: 19rpx;
      .text-1-1 {
        font-size: 17rpx;
        font-family: PingFangSC-Regular;
        color: #ffffff;
        text-align: center;
      }
      .text-1-2 {
        font-family: HannotateSC-W7;
        font-size: 32rpx;
        color: #ffffff;
        text-align: center;
      }
    }
    .text-2 {
      font-family: PingFangSC-Regular;
      font-size: 28rpx;
      color: #333333;
      opacity: 0.75;
      margin: 39rpx 0 0 21rpx;
    }
  }
  .text-t {
    position: absolute;
    top: 0;
    right: 0;
    width: 100rpx;
    height: 100rpx;
    .text-i {
      width: 100%;
      height: 100%;
    }
  }
}
.tiku_type {
  width: 294rpx;
  height: 64rpx;
  background-color: #ded4ca;
  border-radius: 0rpx 0rpx 5rpx 5rpx;
  font-family: PingFangSC-Regular;
  font-size: 26rpx;
  font-weight: normal;
  font-stretch: normal;
  letter-spacing: 0rpx;
  color: #ff0000;
  margin: 20rpx auto;
  line-height: 64rpx;
  text-align: center;
  border-radius: 5rpx;
}
.btn_box {
  display: flex;
  justify-content: space-around;
  margin-top: 30rpx;
  width: 100%;

  .dati_btn {
    width: 222rpx;
    height: 58rpx;
    line-height: 58rpx;
    background-color: #ffbf27;
    border-radius: 5rpx;
    font-family: PingFangSC-Regular;
    font-size: 30rpx;
    font-weight: normal;
    font-stretch: normal;
    letter-spacing: 0rpx;
    color: #ffffff;
  }
}
.btn_text {
  width: 90%;
  text-align: center;
  margin: 0 auto;
  font-size: 26rpx;
  color: #999999;
}
</style>