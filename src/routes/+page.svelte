<script lang="ts">
    import { onMount } from "svelte";
    import Outcome from "./Outcome.svelte";

    let is_loading: boolean = $state(true);

    let wins: number = $state(0);
    let losses: number = $state(0);

    onMount(() => {
        wins = Number(localStorage.getItem("wins"));
        losses = Number(localStorage.getItem("losses"));
        is_loading = false;
    });

    let just_played: boolean = $state(false);
    function toggle_state() {
        just_played = !just_played;
    }

    // 0 - Rock
    // 1 - Paper
    // 2 - Scissors
    let options: number[] = [0, 1, 2];
    let user_choice: number = $state(0);
    let opp_choice: number = $state(
        options[Math.floor(Math.random() * options.length)],
    );

    function num_to_option(option: number): string {
        switch (option) {
            case 0:
                return "Rock 🪨";
            case 1:
                return "Paper 📄";
            case 2:
                return "Scissors ✂️";
            default:
                return "wtf.";
        }
    }

    function random_choice() {
        opp_choice =
            options[Math.floor(Math.random() * options.length)];
    }

    let outcome: "W" | "L" | "D" = $state("D");
    function get_outcome() {
        if (user_choice === opp_choice) {
            outcome = "D";
            return;
        }

        if (
            options[
                (user_choice - 1 + options.length) %
                    options.length
            ] === opp_choice
        ) {
            outcome = "W";
            wins += 1;
            return;
        }

        outcome = "L";
        losses += 1;
    }

    function select(option: number) {
        user_choice = option;
        get_outcome();
        save_winrate();
        toggle_state();
    }

    function save_winrate() {
        localStorage.setItem("wins", String(wins));
        localStorage.setItem("losses", String(losses));
    }
</script>

<div class="main">
    <div class="header">
        <h1 class="title">Rock Paper Scissors</h1>
    </div>

    {#if just_played === false}
        <div class="middle">
            <h2>
                The <span style="color: #bf616a">opponent</span>
                has made a choice.
            </h2>
            <h2>Make your choice:</h2>
        </div>
        <div class="choices">
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
    {:else}
        <div class="middle">
            <h2>
                The <span style="color: #bf616a">opponent</span
                >'s choice was {num_to_option(opp_choice)}.
            </h2>
            <h2>
                You chose {num_to_option(user_choice)}.
            </h2>
            <Outcome {outcome} />
        </div>
        <button
            class="play_again"
            onclick={() => {
                toggle_state();
                random_choice();
            }}>Play again</button
        >
    {/if}

    <div class="footer">
        <div class="stats">
            {#if !is_loading}
                <span>W: {wins}</span>
                <span>|</span>
                <span>L: {losses}</span>
            {:else}
                <span>Loading...</span>
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
    }

    .title {
        width: 50%;
        margin: 0 auto;
        font-weight: 900;
    }

    .middle {
        font-size: 2vh;
    }

    button {
        all: unset;
    }

    .choices > button {
        font-size: 10vh;
        padding: 5vh;
        margin: 2vh;
        background-color: #d8dee9;
        border-radius: 100vh;
        text-shadow: 0px 0px 50px #000000cc;
        cursor: pointer;
        transition: 500ms;
    }

    .choices > button:hover {
        box-shadow: 0px 0px 20px #000000cc;
        transition: 500ms;
    }

    .play_again {
        font-size: 6vmin;
        font-weight: 900;
        text-shadow: none;
        color: #2e3440;
        background-color: #d8dee9;
        padding: 4vh 6vh;
        border-radius: 100vh;
        transition: 500ms;
        cursor: pointer;
    }

    .play_again:hover {
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
    }

    .stats {
        width: 50%;
        margin: 0 auto;
        font-weight: 900;
    }
</style>
