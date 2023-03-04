<script lang="ts">

    import { tweened } from 'svelte/motion';

    function getRandomInt(min: number, max: number) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min) + min); // The maximum is exclusive and the minimum is inclusive
    }

    setInterval(() => {
        $timer++;
    }, 1000);

    let timer = tweened(0)
    let numOne: number;
    let numTwo: number;
    let results: number[];
    let exampleCount: number = -1;
    let exampleSuccess: number = 0;
    let exampleFailed: number = 0;
    let correctResult: number;
    $: elapsedMinutes = Math.floor($timer / 60);
    $: elapsedSeconds = Math.floor($timer - elapsedMinutes * 60);
    let selectedButton = '';
    let toRecap: string[] = [];
    let buttonClass: string[] = ['', '', ''];

    function newExample() {
        numOne = getRandomInt(0, 11);
        numTwo = getRandomInt(0, 11);

        results = [
            getRandomInt(0, 101),
            getRandomInt(0, 101),
            getRandomInt(0, 101)
        ]
        let index = getRandomInt(0, 3);
        results[index] = numOne * numTwo;
        exampleCount++;
        buttonClass = ['','','']
    }

    function sleep(ms:number) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    const handleClick = async (pickedResult:number, index:number) => {
        if (numOne * numTwo == pickedResult) {
            correctResult = 1;
            exampleSuccess++;
            buttonClass[index] = 'success'
        } else {
            correctResult = 0;
            exampleFailed++;
            toRecap = [...toRecap, String(numOne) + ' * ' + String(numTwo) + ' = ' + String(numOne*numTwo)];
            buttonClass[index] = 'fail'
        }
        await sleep(1000);
        newExample();
    };

    newExample();

</script>

<p>{numOne} * {numTwo}</p>
<p>
<button
    on:click={() => handleClick(results[0], 0)}
    class="{buttonClass[0]}"
>{results[0]}</button> /
<button
    on:click={() => handleClick(results[1], 1)}
    class="{buttonClass[1]}"
>{results[1]}</button> /
<button
    on:click={() => handleClick(results[2], 2)}
    class="{buttonClass[2]}"
>{results[2]}</button>
</p>

<p>{exampleCount} příkladů celkem | správně: {exampleSuccess} | na zopakování: {exampleFailed}</p>
<p>Učení si věnoval {elapsedMinutes} minut a {elapsedSeconds} sekund</p>

{#if toRecap.length > 0}
<p>Zopakuj si:</p>
<p>
    {#each toRecap as example}
        {example}<br/>
    {/each}
</p>
{/if}
