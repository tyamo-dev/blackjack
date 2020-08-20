<template>
	<div>
		<div v-if="!addFlag" class="card">
			<div class="left-top">
				<div class="number"> {{ number }} </div>
				<div v-if="mark" class="mark" :class="mark==='heart' || mark==='diamond'? 'mark-red':''"> {{ marker }} </div>
			</div>
			<div  v-if="mark" class="center-mark" :class="mark==='heart' || mark==='diamond'? 'mark-red':''">
				{{ marker }}
			</div>
			<div class="right-down">
				<div  v-if="mark" class="mark" :class="mark==='heart' || mark==='diamond'? 'mark-red':''"> {{ marker }} </div>
				<div class="number"> {{ number }} </div>
			</div>
		</div>

		<div v-else class="card">
			<div class="center-plus-mark">
				+
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: "Card",
	props: {
		number: {
			type: String,
			default: null
		},
		mark: {
			type: String,
			default: null
		},
		addFlag: {
			type: Boolean,
			default: false
		}
	},
	data() {
		return {
			marker: null,
		}
	},
	watch: {
		mark() {
			switch(this.mark) {
				case 'heart':
					this.marker = '❤';
					break;
				case 'spade':
					this.marker = '♠︎';
					break;
				case 'clover':
					this.marker = '♣︎';
					break;
				case 'diamond':
					this.marker = '♦︎';
					break;
			}
			return this.marker;
		}
	},
	created() {
		switch(this.mark) {
			case 'heart':
				this.marker = '❤';
				break;
			case 'spade':
				this.marker = '♠︎';
				break;
			case 'clover':
				this.marker = '♣︎';
				break;
			case 'diamond':
				this.marker = '♦︎';
				break;
		}
	}
	
}
</script>

<style>

.card {
	position: relative;
	height: 170px;
	width: 130px;
	border: 1px solid black;
	border-radius: 5px;
	background: white;
	font-size: 17px;
	cursor: default;
}

.mark-red {
	color: red;
}

.left-top {
	display: flex;
	position: absolute;
	top: 0;
	left: 0;
}

.center-mark {
	position: absolute;
	top: 34%;
	left: 33%;
	font-size: 40px;
}

.center-plus-mark {
	position: absolute;
	top: 31%;
	left: 38%;
	font-size: 40px;
	color: #dfb708;
	font-weight: 500;
}

.right-down {
	display: flex;
	transform: rotateX(180deg);
	position: absolute;
	bottom: 0;
	right: 0;
}


</style>