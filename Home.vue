<template>
	<div class="chat">
		<input type="text" class="chat-text" ref="chatText" @keydown.enter='send' v-model='text'>
		<ul class="chat-list">
			<li class="chat-item" :class="{'self':msg.self}" v-for="msg in messages">{{msg.text}}</li>
		</ul>
	</div>
</template>

<script>
	let socket=io('http://localhost:3000');
	export default{
		name:'chat',
		data(){
			return {
				id:'',
				name:'will',
				text:'',
				messages:[]
			}
		},
		mounted(){
			socket.on('connect',()=>{
				console.log('connect server')

				socket.emit('userInfo',{
					name:this.$data.name
				})
			});
			socket.on('userId',res=>{
				this.$data.id=res.userId
			});
			socket.on('message',msg=>{
				msg.self=msg.id&&msg.id===this.$data.id;
				this.$data.messages.push(msg);
			});
		},
		methods:{
			send(){
				let msg={
					type:1,
					name:'will',
					text:this.$data.text,
				};
				this.$data.text='';
				socket.emit('message',msg);
				// this.$jq.post('http://www.tuling123.com/openapi/api',{
				// 	key:'82a45855ec0d484fbe598d67986d9de2',
				// 	info:msg.text
				// }).then(res=>{
				// 	if(res.code===100000){
				// 		this.$data.messages.push({
				// 			name:'robot',
				// 			text:res.text,
				// 			createdAt:Date.now(),
				// 			self:false
				// 		})
				// 	}
				// });
			}
		}
	}
</script>

<style lang="scss" scoped>
	.chat{
		&-list{
			list-style:none;
			width:200px;
			padding:0;
		}
		&-item{
			background-color: #aaa;
			&.self{
				text-align: right;
				color: #fff;
				background-color: #0a0;
			}
		}
	}
</style>