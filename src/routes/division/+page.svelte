<script lang="ts">

    import { tweened } from 'svelte/motion';
    import { t } from "../../lib/i18n";

    function getRandomInt(min: number, max: number) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min) + min); // The maximum is exclusive and the minimum is inclusive
    }

    setInterval(() => {
        $timer++;
    }, 1000);

    function changeComplexity() {
        if (simple === 1) {
            simple = 0
        } else {
            simple = 1
        }
        newExample()
        exampleCount--
    }

    function findDivisibleNumbers(): [number, number] {
        let num1: number, num2: number;
        if (simple) {
            do {
                num1 = Math.floor(Math.random() * 100) + 1;
                num2 = Math.floor(Math.random() * 10) + 1;
            } while (num1 % num2 !== 0 || num1 === num2);
        } else {
            do {
                num1 = Math.floor(Math.random() * 100) + 1;
                num2 = Math.floor(Math.random() * 90) + 10;
            } while (num1 % num2 !== 0 || num1 === num2);
        }
        return [num1, num2];
    }

    let timer = tweened(0);
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
    let simple:number = 1

    function newExample() {
        let rand = findDivisibleNumbers();
        numOne = rand[0], numTwo = rand[1];

        results = [
            getRandomInt(0, 20),
            getRandomInt(0, 20),
            getRandomInt(0, 20)
        ]
        let index = getRandomInt(0, 3);
        results[index] = numOne / numTwo;
        exampleCount++;
        buttonClass = ['','','']
    }

    function sleep(ms:number) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    const handleClick = async (pickedResult:number, index:number) => {
        if (numOne / numTwo == pickedResult) {
            correctResult = 1;
            exampleSuccess++;
            buttonClass[index] = 'success'
        } else {
            correctResult = 0;
            exampleFailed++;
            toRecap = [...toRecap, String(numOne) + ' ÷ ' + String(numTwo) + ' = ' + String(numOne/numTwo)];
            buttonClass[index] = 'fail'
        }
        await sleep(1000);
        newExample();
    };

    newExample();

</script>

<p>
<label><input checked={simple===1} type=radio on:change={changeComplexity}> {$t("division.simple")}</label>
<label><input checked={simple===0} type=radio on:change={changeComplexity}> {$t("division.complex")}</label>
</p>

<p>{numOne} ÷ {numTwo}</p>

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

<p>{exampleCount} {$t('result.examples')} | {$t('result.correct')}: {exampleSuccess} | {$t('result.to.repeat')}: {exampleFailed}</p>
<p>{@html $t("result.time", { minutes: elapsedMinutes, seconds: elapsedSeconds })}</p>

{#if toRecap.length > 0}
<p>{$t('result.repeat')}</p>
<p>
    {#each toRecap as example}
        {example}<br/>
    {/each}
</p>
{/if}
