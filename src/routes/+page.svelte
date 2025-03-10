<script lang="ts">
    import { onMount } from "svelte";
    import { fade, fly } from "svelte/transition";
    import Outcome from "./Outcome.svelte";
    import { confetti } from "tsparticles-confetti";

    onMount(async () => {
        await loadWinrate();
    });

    // app state
    let isLoading: boolean = $state(true);
    let appState: "selection" | "transition" | "results" =
        $state("selection");
    function toggleState() {
        if (appState === "selection") {
            appState = "transition";
            setTimeout(() => {
                appState = "results";
            }, 550);
        } else {
            appState = "transition";
            setTimeout(() => {
                appState = "selection";
            }, 550);
        }
    }

    let wins: number = $state(0);
    let losses: number = $state(0);
    // load winrate from local storage
    async function loadWinrate() {
        wins = Number(localStorage.getItem("wins"));
        losses = Number(localStorage.getItem("losses"));
        isLoading = false;
    }
    // save winrate to local storage
    function saveWinrate() {
        localStorage.setItem("wins", String(wins));
        localStorage.setItem("losses", String(losses));
    }

    // options
    // 0 - Rock
    // 1 - Paper
    // 2 - Scissors
    let options: number[] = [0, 1, 2];
    let userChoice: number = $state(0);
    function numToOption(option: number): string {
        switch (option) {
            case 0:
                return "Rock 🪨";
            case 1:
                return "Paper 📄";
            case 2:
                return "Scissors ✂️";
            default:
                return "";
        }
    }

    // opponent choice
    let oppChoice: number = $state(
        options[Math.floor(Math.random() * options.length)],
    );
    function randomChoice() {
        oppChoice =
            options[Math.floor(Math.random() * options.length)];
    }

    // user selects option
    function select(option: number) {
        userChoice = option;
        getOutcome();
        saveWinrate();
        toggleState();
    }

    // outcome
    let outcome: "W" | "L" | "D" = $state("D");
    function getOutcome() {
        if (userChoice === oppChoice) {
            outcome = "D";
            return;
        }

        if (
            options[
                (userChoice - 1 + options.length) %
                    options.length
            ] === oppChoice
        ) {
            outcome = "W";
            setTimeout(() => {
                wins += 1;
                throwConfetti();
            }, 800);
            return;
        }

        outcome = "L";
        setTimeout(() => {
            losses += 1;
        }, 800);
    }

    // confetti
    let confettiColors: string[] = [
        "#d08770",
        "#a3be8c",
        "#b48ead",
    ];
    async function throwConfetti() {
        await confetti({
            particleCount: 300,
            spread: 135,
            origin: { x: 0, y: 0.9 },
            colors: confettiColors,
            angle: 45,
            startVelocity: 100,
            decay: 0.85,
            gravity: 0,
            scalar: 1.5,
            zIndex: -1,
            ticks: 500,
        });
        await confetti({
            particleCount: 300,
            spread: 135,
            origin: { x: 1, y: 0.9 },
            colors: confettiColors,
            angle: 135,
            startVelocity: 100,
            decay: 0.85,
            gravity: 0,
            scalar: 1.5,
            zIndex: -1,
            ticks: 500,
        });
    }
</script>

<div class="main">
    <div class="header">
        <h1 class="title">Rock Paper Scissors</h1>
    </div>

    {#if appState === "selection"}
        <div
            class="middle"
            transition:fly={{ duration: 500, y: -100 }}
        >
            <h2>
                The <span style="color: #bf616a">opponent</span>
                has made a choice.
            </h2>
            <h2>Make your choice:</h2>
        </div>
        <div
            class="choices"
            transition:fly={{ duration: 500, y: 100 }}
        >
            <button
                onclick={() => {
                    select(0);
                }}>🪨</button
            >
            <button
                onclick={() => {
                    select(1);
                }}>📄</button
            >
            <button
                onclick={() => {
                    select(2);
                }}>✂️</button
            >
        </div>
    {:else if appState === "transition"}{:else}
        <div
            class="middle"
            in:fade={{ duration: 250 }}
            out:fade={{ duration: 250 }}
        >
            <h2>
                The <span style="color: #bf616a">opponent</span
                >'s choice was {numToOption(oppChoice)}.
            </h2>
            <h2>
                You chose {numToOption(userChoice)}.
            </h2>
            <Outcome {outcome} />
        </div>
        <button
            class="play-again"
            in:fly={{ duration: 500, y: 100 }}
            out:fade={{ duration: 250 }}
            onclick={() => {
                toggleState();
                randomChoice();
            }}>Play again</button
        >
    {/if}

    <div class="footer">
        <div class="stats">
            {#if !isLoading}
                <div
                    class="stats-content"
                    in:fade={{
                        delay: 200,
                        duration: 500,
                    }}
                >
                    <span>W: {wins}</span>
                    <span>|</span>
                    <span>L: {losses}</span>
                </div>
            {:else}
                <span
                    class="stats-content"
                    out:fade={{ duration: 100 }}
                    >Loading...</span
                >
            {/if}
        </div>
    </div>
</div>

<style>
    @import url("https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap");
    * {
        text-align: center;
        font-family: "Montserrat";
        color: #eceff4;
        text-shadow: 5px 5px #00000066;
        user-select: none;
    }

    :global(body) {
        background-color: #2e3440;
        margin: 0;
        padding: 0;
    }

    .main {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
        height: 100vh;
    }

    .header {
        height: 10vh;
        width: 100vw;
        display: flex;
        align-items: center;
        box-shadow: -0px 5px 0px 5px #00000066;
        background-color: #434c5e;
        z-index: 6;
    }

    .title {
        width: 50%;
        margin: 0 auto;
        font-weight: 900;
    }

    .middle {
        z-index: 0;
        font-size: 2vh;
    }

    button {
        all: unset;
        cursor: pointer;
    }

    .choices > button {
        font-size: 10vh;
        padding: 5vh;
        margin: 2vh;
        background-color: #d8dee9;
        border-radius: 100vh;
        text-shadow: 0px 0px 50px #000000cc;
        transition: 500ms;
    }

    .choices > button:hover {
        box-shadow: 0px 0px 20px #000000cc;
        transition: 500ms;
    }

    .play-again {
        font-size: 6vmin;
        font-weight: 900;
        text-shadow: none;
        color: #2e3440;
        background-color: #d8dee9;
        padding: 4vh 6vh;
        border-radius: 100vh;
        transition: 500ms;
    }

    .play-again:hover {
        transition: 500ms;
        box-shadow: 0px 0px 20px #000000cc;
    }

    .footer {
        height: 5vmin;
        width: 100vw;
        display: flex;
        align-items: center;
        background-color: #434c5e;
        color: #eceff4;
        box-shadow: -0px -5px 0px 5px #00000066;
        text-shadow: 5px 5px #00000066;
        z-index: 6;
    }

    .stats {
        position: relative;
        width: 50%;
        height: 100%;
        margin: 0 auto;
        font-weight: 900;
    }

    .stats-content {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 1vmin;
    }
</style>
