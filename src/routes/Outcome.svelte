<script>
    let { outcome } = $props();
</script>

<h1>
    {#if outcome === "W"}
        You <span class="win">won</span>!!!
    {/if}
    {#if outcome === "L"}
        You <span class="loss">lost</span>.
    {/if}
    {#if outcome === "D"}
        It's a <span class="draw">draw</span>.
    {/if}
</h1>

<style>
    h1 {
        font-size: 12vh;
    }

    .win {
        color: #a3be8c;
    }

    .loss {
        color: #bf616a;
    }

    .draw {
        color: #ebcb8b;
    }
</style>
