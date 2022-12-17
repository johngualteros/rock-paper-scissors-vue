<script setup>
	import { ref, computed, onMounted } from "vue";

	const wins = ref(0);
	const losses = ref(0);
	const draws = ref(0);

	const choice = ref(null);
	const computerChoice = ref(null);
	const veredict = ref(null);

	const outcomes = {
		rock: {
			rock: "draw",
			paper: "loss",
			scissors: "win",
		},
		paper: {
			rock: "win",
			paper: "draw",
			scissors: "loss",
		},
		scissors: {
			rock: "loss",
			paper: "win",
			scissors: "draw",
		},
	};

	const winPercentage = computed(() => {
		const total = wins.value + losses.value + draws.value;
		return total === 0 ? 0 : Math.round((wins.value / total) * 100);
	});

	const play = (c) => {
		choice.value = c;
		const choices = ["rock", "paper", "scissors"];
		const random = Math.floor(Math.random() * choices.length);

		computerChoice.value = choices[random];

		const outcome = outcomes[c][computerChoice.value];

		if (outcome === "win") {
			wins.value++;
			veredict.value = "You win!";
		} else if (outcome === "loss") {
			losses.value++;
			veredict.value = "You lose!";
		} else {
			draws.value++;
			veredict.value = "Draw!";
		}

		SaveGame();
	};

	const SaveGame = () => {
		localStorage.setItem("wins", wins.value);
		localStorage.setItem("losses", losses.value);
		localStorage.setItem("draws", draws.value);
	};

	const LoadGame = () => {
		wins.value = localStorage.getItem("wins") || 0;
		losses.value = localStorage.getItem("losses") || 0;
		draws.value = localStorage.getItem("draws") || 0;
	};

	const ResetRound = () => {
		choice.value = null;
		computerChoice.value = null;
		veredict.value = null;
	};

	onMounted(() => {
		LoadGame();
		window.addEventListener("keypress", (e) => {
			if (e.key === "r") {
				ResetRound();
			}
		});
	});
</script>

<template>
	<div class="bg-gray-700 text-white text-center min-h-screen flex flex-col">
		<header class="container mx-auto p-6">
			<h1 class="text-4xl font-bold">Rock, Paper, Scissors</h1>
		</header>

		<main class="container mx-auto p-6 flex-1">
			<div
				v-if="choice === null"
				class="flex items-center justify-center mx-6"
			>
				<button
					@click="play('rock')"
					class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-pink-500 text-black"
				>
					Rock
				</button>
				
        <button
					@click="play('paper')"
					class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-green-500 text-black"
				>
					Paper
				</button>
				
        <button
					@click="play('scissors')"
					class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration-300 hover:bg-yellow-500 text-black"
				>
					Scissors
				</button>
			</div>
      <div v-else>
        <div class="text-3xl mb-4">
          You picked <span class="text-pink-500">{{ choice }}</span>
        </div>
        <div class="text-3xl mb-4">
          You Computer Picked <span class="text-pink-500">{{ computerChoice }}</span>
        </div>
        <div class="text-6xl mb-12">
          {{ veredict }}
        </div>

        <button @click="ResetRound()" class="bg-pink-500 text-lg py-2 px-4">Reset Game</button>
      </div>

      <div class="mt-12 text-3xl mb-4">
        Wins: {{ wins }} - Losses: {{ losses }} - Draws: {{ draws }}
      </div>

      <div class="text-lg">
        Win rate: {{ Math.round(winPercentage) }}%
      </div>
		</main>
	</div>
</template>
