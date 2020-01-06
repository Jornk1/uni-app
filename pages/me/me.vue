<template>
	<view class="content">
		<view class="avatarWrapper">
			<view class="avatar">
				<image class="img" src="../../static/logo.png"></image>
			</view>
		</view> 
		<view class="form">
				<view class="inputWrapper">
					<input type="text" value="" placeholder="请输入用户名" focus/>
					
				</view>
				<view class="inputWrapper">
					<input type="password" placeholder="请输入密码"/>
					
				</view>
				<view class="btnL">
					<text class="btn1">登录</text>
				</view>
				<view class="btnR">
					<text class="btn2">注册</text>
				</view>
				<view class="btnR">
					<text class="btn2">找回密码</text>
				</view>
			</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>
.content{
	width:100vw;
	height: 86vh;
	background-color: #377eb4;
}
.avatarWrapper{
	width:100vw;
	height: 30vh;
	display: flex;
	justify-content: center;
	align-items:flex-end;
}
.avatar{
	width:200upx;
	height: 200upx;
	overflow: hidden;
}
.avatar .img{
	width: 100%;
	height: 100%;
}
.form{
	padding: 0 100upx;
	margin-top: 10px;
}
.inputWrapper{
	width: 100%;
	height: 60upx;
	background: white;
	border-radius: 20px;
	box-sizing: border-box;
	padding: 0 30px;
	margin-top: 15px;
	text-align: center;
}
.inputWrapper .input{
	width: 100%;
	height: 100%;
	font-size: 15px;
}
.btnL{
	width: 100%;
	height: 80upx;
	background: #77b3d7;
	border-radius: 50upx;
	align-items: center;
	display: flex;
	justify-content: center;
	margin: 20px 0;
	
}
.btnL .btn1{
	color:white;
}
.btnR{
	text-align: center;
	color: #eaf6f9;
	font-size: 15px;
	margin-top: 20px;
}
</style>
