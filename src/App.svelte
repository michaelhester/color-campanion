<script>
	//components
	import Loader from "./components/Loader.svelte";
	import TopColor from "./components/TopColor.svelte";
	import OtherColors from "./components/OtherColors.svelte";

	//libraries
	import { csv } from "d3";

	let colors;

	// grab data
	async function getData() {
		let data = await csv("/data/colornames.csv");
		const fakeData = [
			{
				"name": "seafoam greeen",
				"hex": "don't matter"
			},
			{
				"name": "mfil kfe",
				"hex": "don't matter"
			},
			{
				"name": "t esting",
				"hex": "don't matter"
			},
			{
				"name": "fatttt bfdasfofdasy",
				"hex": "don't matter"
			},
		]

		colors = data.map(color => {
			return {
				...color,
				"name": color.name.toLowerCase()
			}
		})
	}

	getData();

	//current input value
	let inputted = "";
	$: inputtedFormatted = inputted.toLowerCase();

	//colors found with input
	$: colorsFound = findColors(inputtedFormatted);
	$: topColor = colorsFound.length > 0 ? colorsFound[0] : null;
	$: otherColors = colorsFound.length > 1 ? colorsFound.slice(1, 10) : [];
	$: check = console.log(otherColors);

	//find all colors
	function findColors(value) {
		if (value.length === 0) return "";

		let foundInOrder = [];
		let foundNoOrder = [];

		for (let i = 0; i < colors.length; i++) {
			const color = colors[i];

			const hasLettersInOrder = checkForLettersInOrder(value, color.name);
			if (hasLettersInOrder) {
				foundInOrder.push({ "letters": hasLettersInOrder, "hex": color.hex });
				continue;
			}

			const hasLettersNoOrder = checkForLettersNoOrder(value, color.name);
			if (hasLettersNoOrder) foundNoOrder.push({ "letters": hasLettersNoOrder, "hex": color.hex });
		}

		const allMatches = foundInOrder.concat(foundNoOrder);
		return allMatches;
	}
	
	//check if letters are in order in input
	function checkForLettersInOrder(lookingFor, lookingIn) {
		//get all letters of word you're looking for
		const letters = lookingFor.split("");
		//letters you successfully found so far
		let lettersFound = [];
		let success = true;

		for (let i = 0; i < letters.length; i++) {
			const letter = letters[i];
			//find index of letter in string you're looking in
			const indexOfLetter = lookingIn.indexOf(letter);

			//if it's in the string
			if (indexOfLetter > -1) {
				//set the substring index to be + 1 of index of the letter, or the end of the string if this is the last letter to find
				let substringIndex = i === letters.length - 1 ? lookingIn.length : indexOfLetter + 1;
				//get letters up to index
				const lettersSoFar = lookingIn.substring(0, substringIndex);
				//format the letters to mark which are the found ones
				const formattedLetters = formatLetters(lettersSoFar, indexOfLetter);
				//console.log(...formattedLetters)
				//push to found letters array
				lettersFound.push(...formattedLetters);
				//cut off string up to index and start again 
				lookingIn = lookingIn.substring(substringIndex);
			} else {
				//break if can't find
				success = false;
				break;
			}
		}

		//if found all letters, return letters
		return success ? lettersFound : false;
	}

	//check if letters are out of order in input
	function checkForLettersNoOrder(lookingFor, lookingIn) {
		//get all letters of word you're looking for
		const letters = lookingFor.split("");
		//comment
		let lettersFound = formatLetters(lookingIn, -1);
		let success = true;

		for (let i = 0; i < letters.length; i++) {
			const letter = letters[i];
			//find index of letter in string you're looking in
			const indexOfLetter = lookingIn.indexOf(letter);

			if (indexOfLetter === -1) {
				success = false;
				break;
			} else {
				lettersFound[indexOfLetter].wasFound = true;
			}
		}

		//if found all letters, return letters
		return success ? lettersFound : false;
	}

	function formatLetters(substring, index) {
		const letters = substring.split("");
		const lettersWithTag = letters.map((letter, i) => { 
			return {
				letter, 
				"wasFound": i === index,
			}
		});
		
		return lettersWithTag;
	}

</script>

<main>
	{#await colors} 
		<Loader />
	{:then}
		<h1>Enter your name and find your color companions!</h1>
		<TopColor color={topColor}/>
		<div class="input--container">
			<input bind:value={inputted} type="text" placeholder="YOUR NAME HERE">
		</div>
		<div class="carousel--container">
			{#if otherColors.length > 0}
				{#each otherColors as otherColor}
					<OtherColors color={otherColor}/>
				{/each}
			{/if}
		</div>
	{/await}

</main>

<style type="text/scss"> 
	h1 {
		text-align: center;
		font-weight: 900;
		font-size: 50px;
	}

	.input {
		&--container {
			margin: auto;
			width: 95%;
			max-width: 600px;
			margin-bottom: 40px;

			input {
				width: 100%;
				text-align: center;
				text-transform: uppercase;
			}
		}
	}

	.carousel  {
    &--container {
      display: flex;
      flex-direction: row;
      flex-wrap: wrap;
      justify-content: center;
    }
	}

</style>