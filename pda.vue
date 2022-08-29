<template>
	<view class="content">{{ scanCode }}</view>
</template>

<script>
var main, receiver, filter;
export default {
	data() {
		return {
			scanCode: '',
			_codeQueryTag: false
		};
	},
	created() {
		this.initScan();
		this.startScan();
	},
	onHide() {
		this.stopScan();
	},
	destroyed() {
		/*页面退出时一定要卸载监听,否则下次进来时会重复，造成扫一次出2个以上的结果*/
		this.stopScan();
	},
	methods: {
		initScan() {
			main = plus.android.runtimeMainActivity(); //获取activity
			var IntentFilter = plus.android.importClass('android.content.IntentFilter');
			filter = new IntentFilter();
			filter.addAction('com.rfid.SCAN'); // 换你的广播动作
			receiver = plus.android.implements(
				'io.dcloud.feature.internal.reflect.BroadcastReceiver',
				{
					onReceive: (context, intent) => {
						plus.android.importClass(intent);
						let code = intent.getStringExtra('scannerdata'); // 换你的广播标签
						this.queryCode(code);
					}
				}
			);
		},
		startScan() {
			main.registerReceiver(receiver, filter);
		},
		stopScan() {
			main.unregisterReceiver(receiver);
		},
		queryCode(code) {
			//防重复
			if (this._codeQueryTag) return false;
			this._codeQueryTag = true;
			setTimeout(function () {
				this._codeQueryTag = false;
			}, 150);
			this.scanCode = code;
			console.log('id:', code);
			uni.$emit('scancodedate', { code: id });
		}
	}
};
</script>

<style>
page {
	background-color: #efeff4;
}
.content {
	text-align: center;
}
</style>
