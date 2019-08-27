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
          <input v-if="!shopInfo" type="text" v-model="urlShop" class="form-control" placeholder="Nhập link shop vào đây" />
          <label  v-if="shopInfo" >{{shopInfo.data.data.shopName}}</label>
        </div>
        <div class="col-sm-3"></div>
      </div>
      <div class="row text-center">
        <div class="col-sm-12">
          <button v-if="!shopInfo" class="btn btn-primary" v-on:click="register">Đăng kí nhận like follow</button>
          <label v-if="shopInfo" >Số lượng follow hiện tại {{shopInfo.data.data.countFollower}}</label>
        </div>
      </div>
      <div class="row text-center" v-if="shopInfo">
          <div class="col-sm-12 text-center">
            <button class="btn btn-primary" v-on:click="addSubAccount">Thêm tài khoản phụ</button>
          <button class="btn btn-primary" v-on:click="register">Đăng kí nhận follow</button>
          <button class="btn btn-primary" v-on:click="register">Đăng kí nhận like</button>
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
    <b-button v-b-modal.modal-1>Launch demo modal</b-button>
  </div>
</template>
<script>
 import axios from 'axios';
export default {
  data: function(){
    return {
      urlShop: '',
      shopInfo: ''
    }
  },
  methods: {
    register (e) {
      axios.post('http://localhost:1337/api/create-shop',{username: this.urlShop})
      .then((data) =>{
        this.shopInfo = data;
      }).catch((err) => {console.log(err)})
    },addSubAccount(){}
  }
}
function checkValidateUrl (url){
  url= 'https://shopee.vn/Sale-Sốc-3-Ngày-Áo-Thun-Trơn-Loại-1-Nhiều-Màu-Nhiều-Size-Loại-Tốt-Giá-Rẻ-i.94065732.21';
  const regex = /shopee.vn\/[^.]*.([0-9]*).[0-9]*/igm;
  const test = regex.test(url);
  if(test){
    const regex1 = /\.([0-9]*)./gm;
    const match = regex1.exec(url);
    return match[1];
    console.log('pass');
  }else{
    console.log('false')
  }

}
</script>
