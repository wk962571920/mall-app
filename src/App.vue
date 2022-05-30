<!--
 * @Author: your name
 * @Date: 2020-09-01 10:12:26
 * @LastEditTime: 2020-09-03 11:38:03
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /miniapp-bodyBuilding/miniapp/src/App.vue
-->
<script>
	export default {
		onLaunch: function() {
			if (!wx.cloud) {

			} else {
				wx.cloud.init({
					env: 'test-env-bcs1s',
					traceUser:true
				})
			}
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	@import "../colorui/main.css"; 
	@import "../colorui/icon.css"; 
	@import "../colorui/app.css";
	page {
		background-color: white;
	}
</style>
