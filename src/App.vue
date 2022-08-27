<template>
  <div class="game" id="app">
		<header class="game__header">
			<div>Correct: <span>{{ answersCorrect }} / {{ answersCorrect + answersIncorrect }}</span></div>
			<div class="game__title">{{ title }}</div>
			<div>Incorrect: <span>{{ answersIncorrect }}</span></div>
		</header>
		<transition-group name="section" tag="main">
			<section class="game__panel game__panel--start" v-if="start" key="1">
				<div class="game__panel-inner">
					<h1 class="game__panel-title">{{ title }}</h1>
					<p class="game__panel-intro">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.</p>
					<button class="game__panel-button" @click="getFlag" type="button">Start</button>
				</div>
			</section>
			<section class="game__panel game__panel--game" v-else-if="game" key="2">
				<div class="game__panel-inner">
					<header class="game__panel-header">
						<h2 class="game__panel-question">What Country's Flag is this?</h2>
					</header>
					<div class="game__panel-flag">
						<img :src="gameCorrect.flags.png" :alt="gameCorrect.name.common" />
					</div>
					<div class="game__panel-form">
						<div class="game__panel-answer" v-for="(item, i) in gameFlags" :key="i">
							<input type="radio" :id="[`answer--${i}`]" :value="item.name.common" name="answers"/>
							<label v-bind:for="[`answer--${i}`]">{{ item.name.common }}</label>
						</div>
					</div>
					<div class="game__panel-footer">
						<button class="game__panel-submit" @click.prevent="checkAnswer" type="button">Check Answer</button>
					</div>
				</div>
				<div class="game__panel-error" v-if="error">Please make a selection!</div>
			</section>
			<section class="game__panel game__panel--result" v-else-if="correctAnswer" key="3">
				<div class="game__panel-inner">
					<header class="game__panel-header">
						<h2 class="game__panel-question"><strong>{{ gameCorrect.name.common }}</strong> is CORRECT!</h2>
					</header>
					<div class="game__panel-correct">
						<img :src="gameCorrect.flags.png" :alt="gameCorrect.name.common" />
					</div>
					<button class="game__panel-button" @click="getFlag" type="button">Next Flag</button>
				</div>
			</section>
			<section class="game__panel game__panel--result" v-else-if="incorrectAnswer" key="4">
				<div class="game__panel-inner">
					<header class="game__panel-header">
						<h2 class="game__panel-question">Inorrect!</h2>
						<div class="game__panel-message">
							<img src="https://i.giphy.com/media/3ohc1h1vy6Gtv4uOLC/giphy.webp" alt="" />
						</div>
					</header>
					<button class="game__panel-button" @click="getFlag" type="button">Next Flag</button>
				</div>
			</section>
			<section class="game__panel" v-else key="5">
				<div class="game__panel-inner">
					<p>END!</p>
				</div>
			</section>
		</transition-group>
	</div>
</template>

<script>

export default {
  name: 'App',
  data() {
		return {
			title: 'Flag Guessing Game',
			flags: [],
			start: true,
			error: false,
			game: false,
			gameFlags: [],
			gameCorrect: null,
			correctAnswer: false,
			incorrectAnswer: false,
			answersCorrect: 0,
			answersIncorrect: 0
		}
	},
	methods: {
		shuffleAnswers(array) {
			var currentIndex = array.length;
			var temporaryValue, randomIndex;
			// While there remain elements to shuffle...
			while (0 !== currentIndex) {
				// Pick a remaining element...
				randomIndex = Math.floor(Math.random() * currentIndex);
				currentIndex -= 1;
				// And swap it with the current element.
				temporaryValue = array[currentIndex];
				array[currentIndex] = array[randomIndex];
				array[randomIndex] = temporaryValue;
			}
			return array;			
		},
		getFlag() {
			let correct = Math.floor(Math.random() * this.gameFlags.length) + 1;	
			for(let i = 0; i < 4; i++) {
				let answers = Math.floor(Math.random() * this.flags.length) + 1;
				this.gameFlags.push(this.flags[answers]);
			}
			this.correctAnswer = false;
			this.incorrectAnswer = false;
			this.start = false;
			this.game = true;
			this.gameCorrect = this.gameFlags[correct];
			this.shuffleAnswers(this.gameFlags);
			console.log('correct answer', this.gameCorrect);
		},
		checkAnswer() {
			const userGuess = this.$el.querySelector('.game__panel-answer input:checked');
			const userGuessVal = userGuess.value;			

			if (userGuessVal.localeCompare(this.gameCorrect.name.common) == 0) {
				this.correctAnswer = true;
				this.game = false;
				this.gameFlags = [];
				this.answersCorrect += 1;
				const index = this.flags.indexOf(this.gameCorrect)
				this.flags.splice(index, 1);
			} else {
				this.incorrectAnswer = true;
				this.game = false;
				this.gameFlags = [];
				this.answersIncorrect += 1;
			}
		}
	},
	created() {
		console.clear();
		fetch('https://restcountries.com/v3.1/all?fields=name,flag,flags')
			.then(res => {
				if(res.ok) {
					return res.json()
				} else {
					return Promise.reject(res);
				}
			})
			.then(data => {
				this.flags = data;
				console.log('flags', this.flags)
			})
	}
}
</script>

<style lang="scss">
// timing 
$base-duration: 250ms;

// Colors
$color1: #1C2321;
$color2: #4E5166;
$color3: #7D98A1;
$color4: #70A9A1;
$color5: #FCAF58;

$success: #27ae60;
$info: #3498db;
$warning: #f1c40f;
$danger: #e74c3c;

$white: #fff;
$black: #000;
$gray: #ecf0f1;
$whitesmoke: whitesmoke;
$text: #555;

// Breakpoints
$sm: 20rem;
$med: 48rem;
$lg: 64rem;

// sizes 
$max-width: 1200px;
$spacer: 1rem;

// fonts / icons
$roboto: 'Roboto', sans-serif;
$robotoCondensed: 'Roboto Condensed', sans-serif;
$awesome: "Font Awesome 5 Free";
$awesomeWeight: 900;

*, *:before, *:after {
	box-sizing: border-box;
	outline: none;
}

html {
	font-family: $roboto;
	font-size: 16px;
	font-smooth: auto;
	font-weight: 300;
	line-height: 1.5;
	color: #444;
}

body {
	position: relative;
	display: flex;
	align-items: center;
	justify-content: center;
	width: 100%;
	height: 100vh;
}

#app {
	display: flex;
	flex-direction: column;
	width: 100vw;
	height: 100vh;
}

.game {
	display: flex;
	align-items: center;
	justify-content: center;
	background-color: rgba($black,0.89);
	
	&::after {
		content: '';
		position: absolute;
		top: 0;
		bottom: 0;
		right: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-attachment: fixed;
		background-image: url('https://images.pexels.com/photos/4468974/pexels-photo-4468974.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260');
		background-position: center center;
		background-repeat: no-repeat;
		background-size: cover;
		z-index: -1;
	}
	&__title {
		// color: $white;
	}
	&__header {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 20px;
		background-color: $white;
		div {
			font-weight: bold;
			&:first-child {
				span {
					color: $success;
				}
			}
			&:last-child {
				span {
					color: $danger;
				}
			}
		}
	}
	&__panel {		
		position: fixed;
		top: 50%;
		left: 50%;
		display: block;
		width: 375px;
		// margin: 20px;
		padding: 70px 40px 40px;
		background-color: $white;
		border-radius: 10px;
		box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
		transform: translate(-50%,-50%);
		z-index: 1;
		@media(min-width: 768px) {
			width: 375px;
		}
		&--start {
			padding: 40px;
		}
		&--result {
			padding-top: 80px;
		}
		&-inner {
			display: flex;
			flex-direction: column;
		}
		&-button {
			display: inline-flex;
			align-items: center;
			justify-content: center;
			margin-left: auto;
			padding: 10px 30px;
			background-color: $color2;
			border: 0;
			border-radius: 99px;
			color: $white;
			transition: 0.25s ease;
			cursor: pointer;
			&::after {
				content: '\f061';
				margin-left: 10px;
				font-family: $awesome;
				font-weight: 900;
				transform: translateX(0);
				transition: 0.25s ease;
			}
			&:hover {
				background-color: $color4;
				&::after {
					transform: translateX(10px);
				}
			}
		}
		&-question {
			position: absolute;
			top: 0;
			left: 0;
			right: 0;
			margin-top: 0;
			padding: 10px;
			background-color: rgba($color2, 0.9);
			border-radius: 10px 10px 0 0;
			color: $white;
			font-size: 20px;
			font-weight: normal;
			text-align: center;
		}
		&-correct,
		&-flag {
			position: relative;
			display: flex;
			align-items: center;
			justify-content: center;
			max-width: 300px;
			margin-bottom: 1.875rem;
			border-radius: 10px;
			box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
			// font-size: 200px;
			overflow: hidden;
			&::after {
				content: '';
				display: block;
				padding-top: 66.6666%;
			}
			img {
				position: absolute;
				top: 0;
				left: 0;
				right: 0;
				bottom: 0;
				width: 100%;
				height: 100%;
				object-fit: cover;
			}
		}
		&-form {
			display: flex;
			flex-direction: column;
			margin-bottom: 1.875rem;
		}
		&-answer {
			flex: 0 0 auto;
			margin-bottom: 2px;
			input {
				display: none;

				&:checked ~ label {
					background-color: $color2;
					color: $white;
					&:before {
						content: '\f00c';
						background-color: rgba($white,0.2);
						color: $success;
						font-family: $awesome;
					}
				}
			}
			label {
				display: flex;
				align-items: center;
				padding: 5px 15px;
				background-color: $whitesmoke;
				border-radius: 5px;
				font-size: 16px;
				font-weight: bold;
				transition: 0.25s ease;
				cursor: pointer;
				&:hover {
					background-color: rgba($color2,0.5);
					color: $white;
					&::before {
						border-color: $white;
					}
				}
				&::before {
					content: '';
					flex: 1 0 20px;
					display: inline-flex;
					align-items: center;
					justify-content: center;
					max-width: 20px;
					height: 20px;
					margin-right: 10px;
					border: 2px solid $color2;
					border-radius: 4px;
					font-size: 12px;
				}
			}
		}
		&-message {
			img {
				max-width: 100%;
			}
		}
		&-footer {
			display: flex;
			align-items: center;
			justify-content: center;
		}
		&-submit {
			display: inline-flex;
			align-items: center;
			justify-content: center;
			padding: 10px 30px;
			background-color: $color3;
			border: 0;
			border-radius: 99px;
			box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
			transition: all 0.3s cubic-bezier(.25,.8,.25,1);
			color: $white;
			cursor: pointer;
			&:hover {
				background-color: $success;
				box-shadow: 0 14px 28px rgba(0,0,0,0.25), 0 10px 10px rgba(0,0,0,0.22);
			}
			&:hover {
				&::after {
					width: 20px;
					margin-left: 5px;
					transform: scale(1);
				}
			}
			&::after {
				content: '\f00c';
				width: 0;
				transform: scale(0);
				transition: 0.25s ease;
				font-family: $awesome;
				font-weight: 900;
			}
		}
	}
}

.section-enter {
	transform: translate(-300%,-50%);
}

.section-enter-active {
	transition: 0.5s ease-in-out;
}

.section-leave-active {
	transition: 0.5s ease-in-out;
}

.section-leave-to {
	transform: translate(300%,-50%);
}
</style>
