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
            v-if="!shopInfo"
            type="text"
            v-model="urlShop"
            class="form-control"
            placeholder="Nhập link shop vào đây"
          />
          <label v-if="shopInfo">{{shopInfo.data.shopName}}</label>
        </div>
        <div class="col-sm-3"></div>
      </div>
      <div class="row text-center">
        <div class="col-sm-12">
          <button
            v-if="!shopInfo"
            class="btn btn-primary button-paren"
            v-on:click="register"
          >Đăng kí nhận like follow</button>
          <label v-if="shopInfo">Số lượng follow hiện tại: {{shopInfo.data.countFollower}}</label>
        </div>
      </div>
      <div class="row text-center" v-if="shopInfo">
        <div class="col-sm-12 text-center">
          <button
            class="btn btn-primary"
            v-on:click="addSubAccount"
            data-toggle="modal"
            data-target="#myModal"
          >Thêm tài khoản phụ</button>
          <button class="btn btn-primary" v-on:click="register">Đăng kí nhận follow</button>
          <button class="btn btn-primary" v-on:click="register">Đăng kí nhận like</button>
        </div>
        <div class="col-sm-12" v-if="addSubAccount"></div>
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
    <div id="myModal" class="modal fade" role="dialog">
      <div class="modal-dialog" id="input">
        <!-- Modal content-->
        <div class="modal-content">
          <div class="modal-header">
            <div class="col-xs-2"></div>
            <div class="col-xs-8">
              <h4>Thêm tài khoản</h4>
            </div>
            <div class="col-xs-2">
              <button type="button" class="close" id="close" data-dismiss="modal">&times;</button>
            </div>
          </div>
          <div class="modal-body">
            <div v-if="objDataLogin.error == 1">
              <div class="form-group">
                <label for="username">Số điện thoại</label>
                <input
                  v-model="username"
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
                  v-model="password"
                  type="password"
                  class="form-control"
                  id="password"
                  placeholder="Nhập mật khẩu"
                  required
                />
              </div>
            </div>
            <div v-if="objDataLogin.error == 0">
              <div class="form-group">
                <label for="exampleInputPassword1">Otp</label>
                <input
                  v-model="objDataLogin.data.otp"
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
            <button v-on:click="login" class="btn btn-primary">Đăng nhập</button>
            <button type="button" class="btn btn-primary" data-dismiss="modal">Close</button>
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
      urlShop: "",
      shopInfo: "",
      showSubAccount: false,
      username: "",
      password: "",
      objDataLogin: { data: {}, error: 1 },
      subAccount: []
    };
  },
  methods: {
    register(e) {
      axios
        .post("http://localhost:1337/api/create-shop", {
          username: this.urlShop
        })
        .then(data => {
          this.shopInfo = data.data;
        })
        .catch(err => {
          console.log(err);
        });
    },
    addSubAccount() {
      this.showSubAccount = true;
    },
    getOTP() {
      if (
        this.username == undefined ||
        this.username == null ||
        this.username == "" ||
        this.password == null ||
        this.password == undefined ||
        this.password == ""
      ) {
        return;
      }
      axios
        .post("http://localhost:1337/api/get-otp", {
          phone: this.username,
          password: this.password
        })
        .then(data => {
          console.log(data);
          this.objDataLogin = data.data;
        })
        .catch(err => {
          console.log(err);
        });
    },
    login() {
      if (
        this.objDataLogin.data.otp == null ||
        this.objDataLogin.data.otp == undefined ||
        this.objDataLogin.data.otp == ""
      ) {
        return;
      }
      axios
        .post("http://localhost:1337/api/login", {
          cookie: this.objDataLogin.data.cookie,
          otp: this.objDataLogin.data.otp,
          token: this.objDataLogin.data.token,
          shopId: this.shopInfo.data.shopId
        })
        .then(data => {
          console.log(data);
          if (data.data.error == 0) {
            $("#myModal").modal("hide");
          }
          this.subAccount.push(data.data);
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};

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
