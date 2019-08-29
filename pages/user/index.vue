<template>
  <div>
    <div class="container" id="index">
      <div class="row" id="header">
        <div class="col-md-3">
          <img class="logo float-right" src="~/assets/images/logo.jpg" />
        </div>
        <div class="col-md-9">
          <h3 class="vertical">Ứng dụng web tăng like,follow shopee free</h3>
        </div>
      </div>
    </div>
    <div class="container" id="input">
      <div class="row">
        <div class="col-sm-3"></div>
        <div class="col-sm-6 text-center">
          <input
            v-if="Object.entries(outputResponse.shopInformation).length === 0 && outputResponse.shopInformation.constructor === Object"
            type="text"
            v-model="inputRequest.url"
            class="form-control"
            placeholder="Nhập link shop vào đây"
          />
          <label v-if="!(Object.entries(outputResponse.shopInformation).length === 0 && outputResponse.shopInformation.constructor === Object)">
            {{this.outputResponse.shopInformation.shopName}}
          </label>
        </div>
        <div class="col-sm-3"></div>
      </div>
      <div class="row text-center">
        <div class="col-sm-12">
          <button  v-if="Object.entries(outputResponse.shopInformation).length === 0 && outputResponse.shopInformation.constructor === Object" class="btn btn-primary button-paren" v-on:click="register">
            Đăng kí nhận like follow
          </button>
          <label v-if="!(Object.entries(outputResponse.shopInformation).length === 0 && outputResponse.shopInformation.constructor === Object)">
            Số lượng follow hiện tại: {{outputResponse.shopInformation.countFollower}}
          </label>
        </div>
      </div>
      <div class="row text-center" v-if="!(Object.entries(outputResponse.shopInformation).length === 0 && outputResponse.shopInformation.constructor === Object)">
        <div class="col-sm-12 text-center">
          <button class="btn btn-primary" data-toggle="modal" data-target="#login">
            Thêm tài khoản phụ
          </button>
          <button class="btn btn-primary" v-on:click="register">Đăng kí nhận follow</button>
          <button class="btn btn-primary" v-on:click="register">Đăng kí nhận like</button>
        </div>
        <div class="col-sm-12" v-if="outputResponse.shopInformation  &&  outputResponse.shopInformation.subAccount && outputResponse.shopInformation.subAccount.length > 0" >
          <ul v-for="(account, index) in outputResponse.shopInformation.subAccount" v-bind:key="account.id">
            <li v-bind="index">{{ account.fullname }}</li>
          </ul>
        </div>
      </div>
      <div class="row" style="margin-top:3em">
        <div class="col-md-1"></div>
        <div class="col-md-4">
          <ul>
            <li>
              <h5>Không cần treo máy</h5>
            </li>
            <li>
              <h5>Không cần tải ứng dụng</h5>
            </li>
            <li>
              <h5>Nhận like, follow miễn phí</h5>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <div id="login" class="modal fade" role="dialog">
      <div class="modal-dialog" id="input">
        <!-- Modal content-->
        <div class="modal-content">
          <div class="modal-header">
            <div class="col-xs-2"></div>
            <div class="col-xs-8">
              <h4>Thêm tài khoản</h4>
            </div>
            <div class="col-xs-2">
              <button type="button" class="close" v-on:click="close" id="close" data-dismiss="modal">&times;</button>
            </div>
          </div>
          <div class="modal-body">
            <div v-if="!check.clickOtp">
              <div class="form-group">
                <label for="username">Số điện thoại</label>
                <input
                  v-model="inputRequest.phone"
                  type="username"
                  class="form-control"
                  id="username"
                  aria-describedby="username"
                  placeholder="Nhập số điện thoại"
                  required
                />
              </div>
              <div class="form-group">
                <label for="exampleInputPassword1">Mật khẩu</label>
                <input
                  v-model="inputRequest.password"
                  type="password"
                  class="form-control"
                  id="password"
                  placeholder="Nhập mật khẩu"
                  required
                />
              </div>
            </div>
            <div v-if="check.clickOtp">
              <div class="form-group">
                <label for="exampleInputPassword1">Otp</label>
                <input
                  v-model="inputRequest.otp"
                  type="text"
                  class="form-control"
                  id="otp"
                  placeholder="Nhập otp"
                  required
                />
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button v-if="!check.clickOtp" v-on:click="getOTP" class="btn btn-primary">Đăng nhập</button>
            <button v-if="check.clickOtp" v-on:click="login" class="btn btn-primary">Đăng nhập</button>
            <button type="button" class="btn btn-primary" v-on:click="close" data-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data: function() {
    return {
      inputRequest: {
        url:'',
        phone: '',
        password:'',
        otp:''
      },
      outputResponse:{
        shopInformation:{},
        otp:{},
        login:{}
      },
      check: {
        clickOtp : false
      }
    };
  },
  methods: {
    register(e) {
      axios
        .post("http://localhost:1337/api/create-shop", {
          username: this.inputRequest.url
        })
        .then(data => {
          if(data != null && data.data != null && data.data.error != null && data.data.error == 0){
            this.outputResponse.shopInformation = data.data.data;
            return;
          }
          return;
        })
        .catch(err => {
          console.log(err);
        });
    },
    getOTP() {
      if (this.inputRequest.phone == null ||  this.inputRequest.phone.trim() == '' || this.inputRequest.password == null || this.inputRequest.password.trim() == ''){
        return;
      }
      axios
        .post("http://localhost:1337/api/get-otp", {
          phone: this.inputRequest.phone,
          password: this.inputRequest.password
        })
        .then(data => {
          if(data != null && data.data != null && data.data.error != null && data.data.error == 0){
            this.outputResponse.otp = data.data.data;
            this.check.clickOtp = !this.check.clickOtp;
          }
          return;
        })
        .catch(err => {
          console.log(err);
        });
    },
    login() {
      if (this.inputRequest.otp == null || this.inputRequest.otp.trim() == ''){
        return;
      }
      axios
        .post("http://localhost:1337/api/login", {
          cookie: this.outputResponse.otp.cookie,
          otp: this.inputRequest.otp,
          token: this.outputResponse.otp.token,
          shopId: this.outputResponse.shopInformation.shopId,
          id: this.outputResponse.shopInformation.id
        })
        .then(data => {
          if (data != null && data.data != null && data.data.error == 0) {
            // this.outputResponse.login = data.data.data;
            this.outputResponse.shopInformation = data.data.data;
            $("#login").modal("hide");
            return;
          }
          return;
        })
        .catch(err => {
          console.log(err);
        });
    },
    close(){
      this.inputRequest = {
        url:'',
        phone: '',
        password:'',
        otp:''
      };
      this.check.clickOtp = false;
    }
  }
};

function init(){

}

function checkValidateUrl(url) {
  url =
    "https://shopee.vn/Sale-Sốc-3-Ngày-Áo-Thun-Trơn-Loại-1-Nhiều-Màu-Nhiều-Size-Loại-Tốt-Giá-Rẻ-i.94065732.21";
  const regex = /shopee.vn\/[^.]*.([0-9]*).[0-9]*/gim;
  const test = regex.test(url);
  if (test) {
    const regex1 = /\.([0-9]*)./gm;
    const match = regex1.exec(url);
    return match[1];
    console.log("pass");
  } else {
    console.log("false");
  }
}
</script>
