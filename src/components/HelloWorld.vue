<template>
	
  <div class="container">
		<img src="../assets/img1.png" class="bg_page_img">

      <div class="item_input">
        <label class="label_name">姓名</label>
				<input class="item_input_style" type="text" placeholder="请输入姓名" v-model="name"/>
      </div>
			
			<div class="item_input">
			  <label class="label_name">邮箱</label> 
				<label class="label_notice">用于接收MT4交易账户</label>
				<input class="item_input_style" type="text" placeholder="请输入邮箱" v-model="email"/>
			</div>
			
      <div class="item_input">
        <label class="label_name">手机号码</label>
        <input class="item_input_style" type="tel" placeholder="点击输入" v-model="tel"/>
      </div>
			
			<div class="item_input">
			  <label class="label_name">银行卡号</label>  
				<label class="label_notice">提现出金时唯一指定账号</label>
			  <input class="item_input_style" type="tel"  placeholder="点击输入" v-model="bank_card"/>
			</div>
			
			<div class="item_input">
			  <label class="label_name">开户银行地址</label>
				<label class="label_notice">例：**银行**分行**支行</label>
			  <input class="item_input_style"   placeholder="点击输入" v-model="bank_address"/>
			</div>
			
			<div class="item_input">
			  <label class="label_name">推荐人代码</label>
				<label class="label_notice">务必真实有效，如无请联系客服</label>
			  <input class="item_input_style" placeholder="点击输入" v-model="recommend_code"/>
			</div>
			
			<div style="margin-top: 1rem; font-size: 1.2rem; color: #378888;">*请上传本人身份证正反面照片*</div>
			
			<button class="button_commit2" @click="chooseType">点击选择照片</button>
			 
			<div  v-for="(url,index) in imgList" :key="index">
				<img class="img_select" :src="url.file.src">
				<img class="img_select_del" src="../assets/delete-.png" @click.stop="delImg(index)"/> 
			</div>
			
			
			<div>
				<!-- <button class="button_commit" @click="confirm">提交</button> -->
				
				<!-- <van-button class="button_commit" type="warning">提交</van-button> -->
			</div>

			<van-button type="default">默认按钮</van-button>
			<van-button type="primary">主要按钮</van-button>
			<van-button type="info">信息按钮</van-button>
			<!-- <van-button type="warning">警告按钮</van-button>
			<van-button type="danger">危险按钮</van-button> -->
	
		<!-- input标签原生样式太丑，所以隐藏 -->
		<input @change="fileChange($event)" type="file" id="upload_file" multiple style="display: none"/> 
		
		<img src="../assets/img2.png" class="bg_page_img" style="margin-top: 1rem;">
  </div>
	
	
</template>

<script>

import axios from "axios";
// import Vue from 'vue'
// import { Button } from 'vant'
// Vue.use(Button)

  export default {
		name: "HelloWorld" ,
    data() {
      return {
				name:"123",
				email:"456",
				tel:"qwe",
				bank_card:"res",
				bank_address:"zxc",
				recommend_code:"ghjk",
				imgList: [],
				size: 0,
				limit:2 //限制图片上传的数量
      }
    },
		components:{

		},
    watch: {
      
    },
    computed: {},
    created() {

// 			axios({
//                 method:'post',
//                 url:'http://192.168.0.108:5000/index',
//                 data:this.qs.stringify({    //这里是发送给后台的数据
//                       name:"aaaaaa",
//                       age:"18",
//                 })
//             }).then((response) =>{          //这里使用了ES6的语法
// 								console.log(response)       //请求成功返回的数据
// 								alert(response)
//             }).catch((error) =>{
// 								console.log(error)       //请求失败返回的数据
// 								alert(error)
//             })
    },
    methods: {
			
      //表单提交
      confirm() {
				
      },
			pickPic(rest){ /*选择照片成功后回调函数*/
          setTimeout(function(){
            Indicator.close();
          },200)
          let that = this;
          let fickedFile = rest.file; // 选中的文件对象
          rest && that.imgList.push(rest.path)
          console.log(rest);
          //... 上传图片逻辑
        },
        deletePic(rest) {
          let that = this;
          let index = that.imgList.indexOf(rest);
          that.imgList.splice(rest, 1)
        },
				chooseType() {
					document.getElementById('upload_file').click();
				 },
				 fileChange(el) {
					if (!el.target.files[0].size) return;
					this.fileList(el.target);
					el.target.value = ''
				 },
				 fileList(fileList) {
					let files = fileList.files;
					for (let i = 0; i < files.length; i++) {
					 //判断是否为文件夹
					 if (files[i].type != '') {
						this.fileAdd(files[i]);
					 } else {
						//文件夹处理
						this.folders(fileList.items[i]);
					 }
					}
				 },
				 //文件夹处理
				 folders(files) {
					let _this = this;
					//判断是否为原生file
					if (files.kind) {
					 files = files.webkitGetAsEntry();
					}
					files.createReader().readEntries(function (file) {
					 for (let i = 0; i < file.length; i++) {
						if (file[i].isFile) {
						 _this.foldersAdd(file[i]);
						} else {
						 _this.folders(file[i]);
						}
					 }
					});
				 },
				 foldersAdd(entry) {
					let _this = this;
					entry.file(function (file) {
					 _this.fileAdd(file)
					})
				 },
				 fileAdd(file) {
					if (this.limit !== undefined) this.limit--;
					if (this.limit !== undefined && this.limit < 0) return;
					//总大小
					this.size = this.size + file.size;
					//判断是否为图片文件
					if (file.type.indexOf('image') == -1) {
					 this.$dialog.toast({mes: '请选择图片文件'});
					} else {
					 let reader = new FileReader();
					 let image = new Image();
					 let _this = this;
					 reader.readAsDataURL(file);
					 reader.onload = function () {
						file.src = this.result;
						image.onload = function(){
						 let width = image.width;
						 let height = image.height;
						 file.width = width;
						 file.height = height;
						 _this.imgList.push({
							file
						 });
						 console.log( _this.imgList);
						};
						image.src= file.src;
					 }
					}
				 },
				 delImg(index) {
					this.size = this.size - this.imgList[index].file.size;//总大小
					this.imgList.splice(index, 1);
					if (this.limit !== undefined) this.limit = 6-this.imgList.length;
				 },
				 displayImg() {
				 }
    }
  }
</script>

<style>
	.container{
		width: 100%;
		background: white;
	}
	.bg_page_img {
		width: 100%;
		margin-bottom: 1rem;
	}
	.item_input {
		width: 100%;
		text-align: left;
	}
	.label_name{
		font-size: 1rem;
		color: #333333;
		margin-left: 0.5rem;
	}
	.label_notice {
		font-size: 0.8rem;
		color: #999999;
		margin-left: 1rem;
	}
	.item_input_style {
		width: 96%;
		height: 8vw;
		margin: 2vw;
		border:0.5px solid #378888;
		border-style:none none solid none;
		background-color:transparent;
		
	}
	.button_commit {
		width: 75%;
		height: 12vw;
		margin: 5vw;
		color: white;
		background: #323435;
		border-style: none;
	}
	
	.button_commit2 {
		width: 75%;
		height: 12vw;
		margin: 5vw;
		color: white;
		background: #378888;
		border-style: none;
	}
	
	.img_select {
		width: 75%;
		height: auto;
		margin-bottom: 1rem;
		margin-top: 0.5rem;
		margin-left: 0.3125rem;
		margin-right: 0.3125rem;
	}
	.img_select_del{
		width: 1.6rem;
		height: 1.6rem;
		position: absolute;
	}

	

</style>
