<template>
	<div id="screen" v-if="isFetchedAcstate">
		<div id="state" v-if="acstate.power">
			<h1>
				{{acstate.temperature}}
			</h1>
			<h2>
				{{acstate.mode}} {{acstate.wind}}
			</h2>
		</div>
		<div v-else>
			<h1>Power is off</h1>
		</div>
	</div>
	<div v-else>
		<h1>
			Loading . . . . . .
		</h1>
	</div>
	<div id="control">
		<button @click="powerbtn()">power</button>
		<div id="pmgroup">
			<button @click="tempp()">+</button>
			<button @click="tempm()">-</button>
		</div>
		<div id="group2">
			<button @click="modex()">mode</button>
			<button @click="windx()">fan</button>
		</div>

	</div>
</template>

<script>
	const Winds = {
		"auto":0,
		"low":1,
		"medium":2,
		"high":3,
	}
	const Modes = {
		"cool": 0,
		"heat": 1,
		"auto": 2,
		"fan": 3,
		"dry": 4,
	}
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
      indexId: {
        type: Number,
        default: 0,
      },
      mac: {
        type:String,
        default: null,
      }
		},
		data() {
			return {
				acstate:{
					power: true,
					temperature: 25,
					wind: "auto",
					mode: "cool",
				},
				isFetchedAcstate: true,
			}
		},
		methods: {
			powerbtn() {
				this.acstate.power = !this.acstate.power
			},
			tempp() {
				if(this.acstate.temperature < 30) {
					this.acstate.temperature++
				}
			},
			tempm() {
				if(this.acstate.temperature > 16) {
					this.acstate.temperature--
				}
			},
			modex() {
				if (this.acstate.mode === "cool") {
				    this.acstate.mode = "heat"
				} else if (this.acstate.mode === "heat") {
				    this.acstate.mode = "auto"
				} else if (this.acstate.mode === "auto") {
				    this.acstate.mode = "fan"
				} else if (this.acstate.mode === "fan") {
				    this.acstate.mode = "dry"
				} else if (this.acstate.mode === "dry") {
				    this.acstate.mode = "cool"
				}
			},
			windx() {
				if (this.acstate.wind === "auto") {
				    this.acstate.wind = "low"
				} else if (this.acstate.wind === "low") {
				    this.acstate.wind = "medium"
				} else if (this.acstate.wind === "medium") {
				    this.acstate.wind = "high"
				} else if (this.acstate.wind === "high") {
				    this.acstate.wind = "auto"
				}
			}
		},
		mounted() {
			fetch("")
		},
		watch: {
			acstate: {
				handler(newValue, oldValue) {
					console.log(oldValue)
					// here send commands to /task
				},
				deep: true,
			},
		},
	}
</script>

<style>
	button
	{
		background-color: #194568;
		color: white;
		font-size: 40px;
    padding: 2% 5%;
    margin-top: 6%;
		margin-left: 6%;
		font-family: 'Times New Roman', Times, serif;
	}
	button:hover
	{
		background-color: #546c8c;
		font-size: 40px;
    padding: 2% 5%;
    margin-top: 6%;
		margin-left: 6%;
	}
	h1 {

	}
	#screen {
		border: #bcc2d7;
		background-color: #194568;
		color: #eff1fe;
		margin-left: 20%;
		margin-right: 20%;
		padding-top: 2%;
		padding-bottom: 2%;
		font-size: 120%;
		height: 20%;
	}
	#app {
		background-color: #eff1fe;
		padding-top: 20%;
		padding-bottom: 20%;
		font-family: 'Times New Roman', Times, serif;
	}
</style>
