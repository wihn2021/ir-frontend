<template>
	<div id="indexlist" v-if="indexs">
		<div v-for="i in indexs">
			<button @click="send(i)" @dblclick="accept(i['id'])" >{{i["id"]}}</button>
      <button @click="accept(i['id'])">accept</button>
		</div>
	</div>
	<div id="brandlist" v-else-if="category">
		<div v-for="br in brands">
			<button @click="brandbtn(br)">{{br["name"]}}</button>
		</div>
	</div>
	<div id="categorylist" v-else>
		<div v-for="c in categories">
			<button @click="catebtn(c)">{{c["name"]}}</button>
		</div>
	</div>
</template>

<script>
	export default {
		props: {
			uid: {
				type: Number,
				default: null,
			},
			token: {
				type: String,
				default: null,
			},
			mac: {
				type: String,
				default: null,
			}
		},
		data() {
			return {
				brand: null,
				category: null,
				brands: null,
				categories: null,
				indexs: null,
			}
		},
		mounted() {
			fetch("/irext-server/indexing/list_categories", {
				method: "POST",
				headers: {
				    "Content-Type": "application/json",
				},
				body: JSON.stringify(
				{
					"id": this.uid,
					"token": this.token,
					"from": 0,
					"count": 100,
				}),
			}).then(resp => resp.json()).then((result) => {
				this.categories = result["entity"]
			})
		},
		methods: {
			brandbtn(b) {
				this.brand = b
				fetch("/irext-server/indexing/list_indexes", {
					method: "POST",
					headers: {
						"Content-Type": "application/json",
					},
					body: JSON.stringify(
					{
						"id": this.uid,
						"token": this.token,
						"from": 0,
						"count": 100,
						"categoryId": this.category["id"],
						"brandId": this.brand["id"],
					})
				}).then(resp => resp.json()).then(res => {
					this.indexs = res["entity"]
					console.log(res)
				})
			},
			catebtn(c) {
				this.category = c
				fetch("/irext-server/indexing/list_brands", {
					method: "POST",
					headers: {
					    "Content-Type": "application/json",
					},
					body: JSON.stringify(
					{
						"id": this.uid,
						"token": this.token,
						"categoryId": c["id"],
						"from": 0,
						"count": 100,
					}),
				}).then(resp => resp.json()).then((res) => {
					this.brands = res["entity"]
				})
			},
			send(i) {
				fetch("/irext-server/operation/decode", {
					method: "POST",
					headers: {
					    "Content-Type": "application/json",
					},
					body: JSON.stringify({
						"id": this.uid,
						"token": this.token,
						"indexId": i["id"],
						"keyCode": 1,
						"acStatus": {
							"acPower":"0",
							"acMode":"0",
							"acTemp":"4",
							"acWindSpeed":"0",
							"acWindDir":"0"
						},
						"changeWindDir": 0,
						"paraData": 0,
					})
				}).then(resp => resp.json()).then(res => {
					fetch("/task", {
						method: "POST",
						headers: {
							"Content-Type": "application/json",
						},
						body: JSON.stringify({
							"mac": this.mac,
							"commandArgs": res["entity"],
						})
					})
				})
			},
			accept(i) {
				let n = prompt("给它取一个名字")
				fetch("/deviceList", {
					method: "PUT",
					headers: {
						"Content-Type": "application/json",
					},
					body: JSON.stringify({
						"mac": this.mac,
            "indexId": i,
            "name": n,
            "isKnown": true
					})
				}).then(() => {
          alert("设置更新成功")
        }).catch(() => {
          alert("设置更新失败")
        })
			}
		},

	}
</script>

<style>
</style>
