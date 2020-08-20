<template>
	<div class="modal">
		<div class="selectModal">
			<!-- i wanna refactor this code -->
			<span id="ace"   @click="clickNum('A')"  :class="[activeNum==='A'  ? 'activeNum': '',noExistNum.indexOf('A') > -1 ? 'noExist':'']">A</span>
			<span id="two"   @click="clickNum('2')"  :class="[activeNum==='2'  ? 'activeNum': '',noExistNum.indexOf('2') > -1 ? 'noExist':'']">2</span>
			<span id="three" @click="clickNum('3')"  :class="[activeNum==='3'  ? 'activeNum': '',noExistNum.indexOf('3') > -1 ? 'noExist':'']">3</span>
			<span id="four"  @click="clickNum('4')"  :class="[activeNum==='4'  ? 'activeNum': '',noExistNum.indexOf('4') > -1 ? 'noExist':'']">4</span>
			<span id="five"  @click="clickNum('5')"  :class="[activeNum==='5'  ? 'activeNum': '',noExistNum.indexOf('5') > -1 ? 'noExist':'']">5</span>
			<span id="six"   @click="clickNum('6')"  :class="[activeNum==='6'  ? 'activeNum': '',noExistNum.indexOf('6') > -1 ? 'noExist':'']">6</span>
			<span id="seven" @click="clickNum('7')"  :class="[activeNum==='7'  ? 'activeNum': '',noExistNum.indexOf('7') > -1 ? 'noExist':'']">7</span>
			<span id="eight" @click="clickNum('8')"  :class="[activeNum==='8'  ? 'activeNum': '',noExistNum.indexOf('8') > -1 ? 'noExist':'']">8</span>
			<span id="nine"  @click="clickNum('9')"  :class="[activeNum==='9'  ? 'activeNum': '',noExistNum.indexOf('9') > -1 ? 'noExist':'']">9</span>
			<span id="ten"   @click="clickNum('10')" :class="[activeNum==='10' ? 'activeNum': '',noExistNum.indexOf('10') > -1 ? 'noExist':'']">10</span>
			<span id="jack"  @click="clickNum('J')"  :class="[activeNum==='J'  ? 'activeNum': '',noExistNum.indexOf('J') > -1 ? 'noExist':'']">J</span>
			<span id="queen" @click="clickNum('Q')"  :class="[activeNum==='Q'  ? 'activeNum': '',noExistNum.indexOf('Q') > -1 ? 'noExist':'']">Q</span>
			<span id="king"  @click="clickNum('K')"  :class="[activeNum==='K'  ? 'activeNum': '',noExistNum.indexOf('K') > -1 ? 'noExist':'']">K</span>

			<div class="selectMark">
				<span id="heart"   @click="clickMark('heart',0)"   :class="[activeMark==='heart'  ? 'activeMark': '', noExistMarker.heart? 'noExist': '']">❤︎</span>
				<span id="spade"   @click="clickMark('spade',1)"   :class="[activeMark==='spade'  ? 'activeMark': '', noExistMarker.spade? 'noExist': '']">♠︎</span>
				<span id="clover"  @click="clickMark('clover',3)"  :class="[activeMark==='clover' ? 'activeMark': '', noExistMarker.clover? 'noExist': '']">♣︎</span>
				<span id="diamond" @click="clickMark('diamond',2)" :class="[activeMark==='diamond'? 'activeMark': '', noExistMarker.diamond? 'noExist': '']">♦︎</span>
			</div>
		</div>
		<div class="selectingCard">
			<div class="roll rollTitle">
				Select Roll
			</div>
			<div class="roll rollName">
				{{ roll ? roll : 'no roll'}}
			</div>
			<Card :mark="activeMark" :number="activeNum" class="selectCard"/>
			<div class="card-decision-btn" @click="clickOk()"> O K </div>
		</div>
	</div>
</template>

<script>
import Card from '@/components/Card.vue'

export default {
	name: 'CardSelector',
	components: {
		Card
	},

	props: {
		deck: {
			type: Array,
			default: () => ([])
		},
		roll: {
			type: String,
			default: null
		},
		dealerPoint: {
			type: String,
			default: '0'
		}
	},
	data() {
		return {
			activeNum: null,
			activeMark: null,
			markList: ['heart','spade','diamond','clover'],
			numList: ['A','2','3','4','5','6','7','8','9','10','J','Q','K'],
			noExistMarker: {},
			noExistNum: [],
		}
	},
	methods: {
		clickNum(num) {
			this.activeNum = num;
			this.noExistMarker = {};

			let selectNum = String(num) + 's';
			for (let i=0; this.markList.length > i; i++ ) {
				for (let j=0; this.numList.length > j; j++) {
					if(this.deck[i][j] === selectNum) {
						this.noExistMarker[this.markList[i]] = this.markList[i];
					}
				}
			}
		},
		clickMark(mark, idx) {
			this.activeMark = mark;
			this.noExistNum = [];
			for(let i=0; this.deck[idx].length > i; i++) {
				if(this.deck[idx][i].slice(-1) === 's') {
					this.noExistNum.push(this.numList[i]);
				}
			}
		},
		clickOk() {
			if(!this.roll) {
				alert("please select roll");
				return;
			}
			if (!this.activeNum && !this.activemark) {
				alert('please select mark & number') 
				return;
			} else {
				if (!this.activeNum) alert('please select number');
				if (!this.activeMark) alert('please select mark');
			}
			
			if (this.roll === 'dealer' && this.dealerPoint >= 17) {
				alert("dealer point is 17 over");
				return;
			} else {
				this.$emit("giveCard",this.activeNum,this.activeMark);
				this.activeNum = null;
				this.activeMark = null;
			}
		}
	}

}
</script>

<style>

.modal {
	position: relative;
	display: flex;
	width: 600px;
	margin: 0 auto;
}

.rollTitle {
	color: black;
	font-size: 16px;
	margin: 10px 0 10px 20px;
}

.rollName {
	color: blue;
	font-size: 15px;
	margin: 10px 0 10px 39px;
}

.selectCard {
	margin-top: 20px;
	margin-right: 82px;
}

.card-decision-btn {
	width: 30%;
	margin: 20px 0 0 35px;
	border-radius: 10px;
	background: red;
	color: white;
	opacity: 0.3;
	text-align: center;
}

.card-decision-btn:hover {
	opacity: 1;
}

/* Num Css Start */
.selectModal span{
  position: absolute;
  font-size: 2em;
  width: 45px;
  line-height: 1em;
  text-align:center;
  left: 50%;
  top: 50%;
  transform-origin: left top;
  cursor: default;
}

.selectModal {
	position: relative;
	width: 300px;
	height: 300px;
	border-radius: 50%;
	margin: 0 30px 0 60px;
	border: 2px solid #333;
}

.selectModal span:hover {
	color: #c76700;
}

.activeNum {
	color: #c76700;
}

#ace{
	transform: rotate(0deg) translate(0, -120px) rotate(-0deg) translate(-50%,-50%); 
}
#two{
	transform: rotate(27deg) translate(0, -120px) rotate(-27deg) translate(-50%,-50%); 
}
#three{
	transform: rotate(54deg) translate(0, -120px) rotate(-54deg) translate(-50%,-50%); 
}
#four{
	transform: rotate(81deg) translate(0, -120px) rotate(-81deg) translate(-50%,-50%); 
}
#five{
	transform: rotate(108deg) translate(0, -120px) rotate(-108deg) translate(-50%,-50%); 
}
#six{
	transform: rotate(135deg) translate(0, -120px) rotate(-135deg) translate(-50%,-50%); 
}
#seven{
	transform: rotate(162deg) translate(0, -120px) rotate(-162deg) translate(-50%,-50%); 
}
#eight{
	transform: rotate(189deg) translate(0, -120px) rotate(-189deg) translate(-50%,-50%); 
}
#nine{
	transform: rotate(216deg) translate(0, -120px) rotate(-216deg) translate(-50%,-50%); 
}
#ten{
	transform: rotate(243deg) translate(0, -120px) rotate(-243deg) translate(-50%,-50%); 
}
#jack{
	transform: rotate(270deg) translate(0, -120px) rotate(-270deg) translate(-50%,-50%); 
}
#queen{
	transform: rotate(297deg) translate(0, -120px) rotate(-297deg) translate(-50%,-50%); 
}
#king{
	transform: rotate(330deg) translate(0, -120px) rotate(-330deg) translate(-50%,-50%);  
}

/* Num Css End */

/* Mark Css Start */
.selectMark {
	position: absolute;
	top: 70px;
	left: 70px;
	width: 150px;
	height: 150px;
	border-radius: 50%;
	margin: 0 auto;
	border: 2px solid #333;
}

.selectMark span{
	position: absolute;
	opacity: 0.4;
}

.selectMark span:hover {
	opacity: 1;
}

.selectMark .activeMark {
	opacity: 1;
}

#heart {
	top: 30px;
	left: 20px;
	color: red;
}

#spade {
	top: 30px;
	left: 80px;
	color: black;
}

#clover {
	top: 90px;
	left: 20px;
	color: black;
}

#diamond {
	top: 90px;
	left: 80px;
	color: red;
}

.noExist {
	color: gainsboro !important;
	pointer-events: none;
}
/* Mark Css End */
</style>