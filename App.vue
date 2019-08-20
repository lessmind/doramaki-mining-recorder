<template>
	<div>
		<table>
			<tr>
				<td :key="index"
					v-for="(direction, index) in directionList"
					class="direction" :class="{selected: direction.name === currentDirection}"
					@click="setDirection(direction.name)">
					<img v-if="direction.name" :src="getImage(direction.name)">
				</td>
			</tr>
		</table>
		<table class="mine-table">
			<tr :key="index" v-for="(row, index) in mineTable">
				<td :key="mineI" v-for="(mine, mineI) in row"
					class="mine" :class="{selected: anchor[0] === mineI && anchor[1] === index}"
					@click="setAnchor(mineI, index)">
					<img v-if="mine" :src="getImage(mine)">
				</td>
			</tr>
		</table>
		<table class="input-panel">
			<tr :key="index" v-for="(row, index) in inputTable">
				<td :key="btnI" v-for="(btn, btnI) in row" class="input-map" @click="setMine(btn.name)">
					<span v-if="btn.key" class="mapping-key">"{{btn.key}}"</span>
					<img v-if="btn.name" :src="getImage(btn.name)">
				</td>
			</tr>
		</table>
	</div>
</template>
<script>
/* eslint-disable */
import Vue from 'vue';
const directions = {
	trl: {
		start: [6, 0],
		next: [-1, 0],
		jump: [6, 1],
	},
	tld: {
		start: [0, 0],
		next: [0, 1],
		jump: [1, -6],
	},
	blr: {
		start: [0, 6],
		next: [1, 0],
		jump: [-6, -1],
	},
	bru: {
		start: [6, 6],
		next: [0, -1],
		jump: [-1, 6],
	},
	blu: {
		start: [0, 6],
		next: [0, -1],
		jump: [1, 6],
	},
	brl: {
		start: [6, 6],
		next: [-1, 0],
		jump: [6, -1],
	},
	trd: {
		start: [6, 0],
		next: [0, 1],
		jump: [-1, -6],
	},
	tlr: {
		start: [0, 0],
		next: [1, 0],
		jump: [-6, 1],
	},
};
const directionList = [
	'tlr',
	'tld',
	'trl',
	'trd',
	'blr',
	'blu',
	'bru',
	'brl',
];
const keys = [
	'q',
	'w',
	'e',
	'r',
	'a',
	's',
	'd',
	'f',
	'z',
	'x',
	'c',
	'v',
	'b',
	' ',
	null,
];
const materials = [
	'ruby',
	'diamond',
	'emerald',
	'sapphire',
	'amber',
	'stone',
	'copper',
	'iron',
	'silver',
	'gold',
	'garakuta',
	'fossil',
	'stair',
	'empty',
	null,
];
export default {
	mounted() {
		window.addEventListener('keydown', event => {
			this.handleKeyboard(event);
		});
	},
	data() {
		return {
			anchor: [0, 0],
			currentDirection: 'tlr',
			mineTable: [
				[null, null, null, null, null, null, null],
				[null, null, null, null, null, null, null],
				[null, null, null, null, null, null, null],
				[null, null, null, null, null, null, null],
				[null, null, null, null, null, null, null],
				[null, null, null, null, null, null, null],
				[null, null, null, null, null, null, null],
			],
		};
	},
	methods: {
		handleKeyboard(event) {
			for(let i in keys) {
				if(keys[i] === event.key) {
					this.setMine(materials[i]);
				}
			}
		},
		setAnchor(x, y) {
			this.anchor = [x, y];
		},
		setMine(name) {
			let [x, y] = this.anchor;
			if(
				x < 0 ||
				x >= 7 ||
				y < 0 ||
				y >= 7
			) {
				return;
			}
			this.$set(this.mineTable[y], x, name);
			this.$set(this.mineTable, y, this.mineTable[y]);

			let [nextX, nextY] = directions[this.currentDirection].next;
			let [jumpX, jumpY] = directions[this.currentDirection].jump;
			if(
				(x + nextX) < 0 ||
				(x + nextX) >= 7 ||
				(y + nextY) < 0 ||
				(y + nextY) >= 7
			) {
				this.anchor = [x + jumpX, y + jumpY];
			}
			else {
				this.anchor = [x + nextX, y + nextY];
			}
		},
		getImage(name) {
			return process.env.BASE_URL + 'images/' + name + '.jpg';
		},
		clearMineTable() {
			for(let i = 0; i < 7; i++) {
				for(let k = 0; k < 7; k++) {
					this.$set(this.mineTable[i], k, null);
					this.$set(this.mineTable, i, this.mineTable[i]);
				}
			}
		},
		setDirection(name) {
			if(confirm('Are you sure to clear mine table and start mining in new direction?')) {
				this.currentDirection = name;
				this.anchor = [].concat(directions[this.currentDirection].start);
				this.clearMineTable();
			}
		}
	},
	computed: {
		inputTable() {
			const table = [];
			let startRow = -1;
			for(let i in materials) {
				if(!table.length || table[startRow].length % 5 === 0) {
					startRow++;
					table.push([]);
				}
				table[startRow].push({
					name: materials[i],
					key: keys[i],
				});
			}
			return table;
		},
		directionList() {
			return directionList.map(d => Object.assign({name: d}, directions[d]));
		},
	}
}
</script>
<style>
.direction {
	width: 3em;
	height: 3em;
}
.direction.selected {
	border-style: solid;
	border-width: 0.2em;
	border-color: red;
}
.direction img {
	width: 100%;
	height: 100%;
}
.mine-table .mine.selected {
	border-color: red;
}
.mine-table .mine {
	width: 3em;
	height: 3em;
	border-style: solid;
	border-width: 0.2em;
}
.mine-table .mine img {
	width: 100%;
	height: 100%;
}
.input-panel .input-map {
	width: 5em;
	height: 5em;
}
.input-panel .input-map img {
	width: 100%;
	height: 100%;
}
.input-panel .mapping-key {
	background-color: #20e0d7d5;
	padding: 8px;
	line-height: 5px;
	display: inline-block;
}
</style>
