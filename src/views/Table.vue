<template>
	<div class="mainWindow">
		<div id="dealer">
			<div class="person-header">
				<div class="player-roll">Dealer</div>
			</div>
			<div class="myHand">
				<div v-for="(card, key) in dealerHand" :key="key">
					<Card :number="card.number" :mark="card.mark" class="myCard"/>
				</div>
				<div class="dealer-card" @click="setRoll('dealer')">
					<Card v-if="dealerPoint < 17" :number="null" :mark="null" :add-flag="true" class="myCard"/>
				</div>
			</div>
			<!-- <Paticipant :hand="firstDealerHand"/> -->
		</div>
		<div id="player">
			<div class="person-header">
				<div class="player-roll">Player</div>
			</div>
			<div class="myHand">
				<div v-for="(card, key) in playerHand" :key="key">
					<Card :number="card.number" :mark="card.mark" class="myCard"/>
				</div>
				<div class="player-card" @click="setRoll('player')" >
					<Card v-if="playerPoint < 21" :number="null" :mark="null" :add-flag="true" class="myCard"/>
				</div>
			</div>
			<!-- <Paticipant :hand="firstPlayerHand"/> -->
		</div>
		<div class="notation">
			※めんどくさかったので、Ace = 1 としかカウントされません...orz 後でやる...
		</div>
		<div class="table-bottom">
			<div class="player-info">

				<div class="table-bottom-title"> Player Info </div>
				<div class="target-point table-bottom-content">
					<div>Next Target Point</div>
					<select class="current-point-area"  @change="onChange($event, 'player')">
						<option v-for="point in targetPointList" :key="point" :value="point">
							{{ point }}
						</option>
					</select>	
				</div>
				<div class="current-point table-bottom-content">
					<div>Current Point</div>
					<div class="current-point-area" :class="playerPoint===21 ? 'bj' : ''">
						{{ playerPoint || 0 }}
					</div>
				</div>
				<div class="probability table-bottom-content">
					<div>Drawing Probability</div>
					<div class="prob-score current-point-area">
						{{ playerProb }}<span>%</span>
					</div>
				</div>

				<div class="burst-text" :class="playerPoint > 21 ? 'burst':''"> Burst !!</div>
			</div>

			<div class="dealer-info">
				<div class="table-bottom-title"> Dealer Info </div>
				<div class="target-point table-bottom-content">
					<div>Next Target Point</div>
					<select class="current-point-area" @change="onChange($event, 'dealer')">
						<option v-for="point in targetPointList" :key="point" :value="point">
							{{ point }}
						</option>
					</select>	
				</div>
				<div class="current-point table-bottom-content">
					<div>Current Point</div>
					<div class="current-point-area" :class="dealerPoint===21 ? 'bj' : ''">
						{{ dealerPoint || 0}}
					</div>
				</div>
				<div class="probability table-bottom-content">
					<div>Drawing Probability</div>
					<div class="prob-score current-point-area">
						{{ dealerProb }}<span>%</span>
					</div>
				</div>

				<div class="burst-text"> Burst !!</div>
			</div>
			<CardSelector :deck=deck :roll="selectRoll" :dealer-point="String(dealerPoint)" class="card-selector" @giveCard="getCard"/>
		</div>
	</div>
</template>

<script>
import CardSelector from '../components/CardSelector.vue'
import Card from '../components/Card.vue'

export default {
	name: 'Table',
	components: {
		Card,
		CardSelector
	},
	data() {
		return {
			deck: [
				['A','2','3','4','5','6','7','8','9','10','J','Q','K'], //♡
				['A','2','3','4','5','6','7','8','9','10','J','Q','K'], //♤
				['A','2','3','4','5','6','7','8','9','10','J','Q','K'], //♢
				['A','2','3','4','5','6','7','8','9','10','J','Q','K']  //♧
			],
			dealerHand: [],
			playerHand: [],
			selectRoll: null,
			targetPointList: [17, 18, 19, 20, 21],
			currentPoint: 17,
			probability: 60,
			dealerInfo: {'point':'0', 'target':'17', 'prob':'0'},
			playerInfo: {'point':'0', 'target':'17', 'prob':'0'}
		}
	},

	watch: {
		dealerHand() {
			let point = 0;
			for (let i=0; i < this.dealerHand.length; i++) {
				point += parseInt(this.dealerHand[i].calc)
			}
			this.dealerInfo.point = point;
		},
		playerHand() {
			let point = 0;
			for (let i=0; i < this.playerHand.length; i++) {
				point += parseInt(this.playerHand[i].calc)
			}
			this.playerInfo.point = point;
		},

		
	},

	computed: {
		dealerPoint() {
			return this.dealerInfo.point;
		},
		playerPoint() {
			return this.playerInfo.point;
		},
		dealerProb() {
			return this.playerInfo ? this.calcProb(this.dealerInfo.point, this.dealerInfo.target) : 0;
		},
		playerProb() {
			return this.dealerInfo ? this.calcProb(this.playerInfo.point, this.playerInfo.target) : 0;
		}
	},

	methods: {
		getCard(num, mark) {
			let card = {};
			let markIdx = '';
			let numIdx = '';
			let calcNum = '';

			//delete card in deck
			switch(mark) {
				case 'heart':
					markIdx = 0;
					break;
				case 'spade':
					markIdx = 1;
					break;
				case 'diamond':
					markIdx = 2;
					break;
				case 'clover':
					markIdx = 3;
					break;
			}

			switch(num) {
				case 'A':
					numIdx = 1;
					calcNum = 1;
					break;
				case 'J':
					numIdx = 11;
					calcNum = 10;
					break;
				case 'Q':
					numIdx = 12;
					calcNum = 10;
					break;
				case 'K':
					numIdx = 13;
					calcNum = 10;
					break;
				default:
					numIdx = num;
					calcNum = num;
					break;
			}

			card.number = String(num);
			card.mark = String(mark);
			card.calc = String(calcNum);

			this.deck[markIdx][numIdx-1] = this.deck[markIdx][numIdx-1] + 's';
			if (this.selectRoll) {
				if(this.selectRoll === 'dealer') {
					this.dealerHand.push(card);
				} else {
					this.playerHand.push(card);
				}	
			}
		},
		setRoll(roll) {
			this.selectRoll = roll;
		},
		onChange(e, roll) {
			if(roll === 'dealer') this.dealerInfo.target = e.target.value;
			if(roll === 'player') this.playerInfo.target = e.target.value;
		},
		calcProb(point, target) {
			let sabun = target - point;

			if (sabun <= 0) {
				return 0;
			}
			/* card count of be able to draw */
			let cardCnt = 0;
			let nokoriCnt = 0;
			let prob = 0;
			let num = 0;

			for(let i=0; i < this.deck.length; i++) {
				for(let j=0; j < this.deck[i].length; j++) {
					if (this.deck[i][j].slice(-1) !== 's') {
						cardCnt += 1;
					}
					//change pic to number
					switch (this.deck[i][j]) {
						case 'A':
							num = 1;
							break;
						case 'J','Q','K':
							num = 10;
							break;
						default:
							num = this.deck[i][j];
							break;
					}
					if (num == sabun) {
						nokoriCnt += 1;
					}
				}
			}

			prob = (parseInt(nokoriCnt) /parseInt(cardCnt) * 100).toFixed(1);

			return prob;
		}
	}

}
</script>

<style >

.mainWindow {
	height: 100vh;
	width: 100vw;
}

.myHand {
	display: flex;
	flex-wrap: wrap;
	margin-top: 15px;
}

.myCard {
	margin-left: 5px;
	margin-bottom: 5px;
}

.no-select {
	height: 150px;
	width: 100px;
	margin: 15px 10px;
	border: 1px solid black;
	border-radius: 10px;
	color: #560169;
	cursor: default;
	text-align: center;
}

.selectCard:hover {
	opacity: 0.4;
}

#dealer {
	margin: 0 30px;
}
#player {
	margin: 30px 30px 10px 30px;
}
.person-header {
	display: flex;
}

.notation {
	color: red;
	margin-left: 60px;
}

/* table bottom contents CSS start*/

.table-bottom {
	display: flex;
	width: 100%;
	border-top-style: 3px solid black;
	z-index: 100;
}

.player-info {
	width: 23%;
	height: 75%;
	text-align: center;
	border: 2px solid #9b2020;
	border-radius: 5px;
	margin-left: 60px;
	margin-top: 20px;
}

.dealer-info {
	width: 23%;
	height: 75%;
	text-align: center;
	margin-left: 20px;
	border: 2px solid #9b2020;
	border-radius: 5px;
	margin-top: 20px;
}

.table-bottom-title {
	text-align: center;
	margin: -17px auto 30px auto;
	font-size: 20px;
	width: 50%;
	background: white;
}

.card-selector {
	margin-bottom: 10px;
	width: 55%
}

.table-bottom-content {
	display: flex;
	margin: 20px auto;
	width:  230px;
}

.target-point select {
	display: block;
	width: 52px;
	height: 30px;
}

.current-point-area {
	display: inline-block;
	border: 1px solid rgba(0, 0, 0, 0.466);
	border-radius: 3px;
	width: 50px;
	text-align: center;
	margin-left: auto;
	padding-top: 2px;
	cursor: default;
	font-size: 14px;
}

.prob-score span {
	font-size: 12px;
	padding-left: 2px;
}

.burst-text {
	color: red;
	opacity: 0.2;
	font-size: 17px;
	font-weight: bold;
	margin-top: 30px;
	margin-bottom: 30px;
}

.burst {
	opacity: 1;
}

.bj {
	border: 2px solid #d15e00;
	background: #f7e706bd;
}
/* table bottom content CSS end*/

</style>