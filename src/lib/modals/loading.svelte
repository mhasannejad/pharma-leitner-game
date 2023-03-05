<script>
    import { closeModal } from 'svelte-modals'

    // provided by <Modals />
    export let isOpen

    export let title
    export let message

</script>

{#if isOpen}
    <div role="dialog" class="modal">
        <div class="contents">
            <p>loading ... </p>
            <p>sorry, its free server and i dont have money to buy a good one.!</p>
        </div>
    </div>
{/if}

<style>
    .modal {
        position: fixed;
        top: 0;
        bottom: 0;
        right: 0;
        left: 0;
        display: flex;
        justify-content: center;
        align-items: center;

        /* allow click-through to backdrop */
        pointer-events: none;
    }

    .contents {
        min-width: 240px;
        border-radius: 6px;
        padding: 16px;
        background: rgba(0,0,0,0.9);
        display: flex;
        color: white !important;
        flex-direction: column;
        justify-content: space-between;
        pointer-events: auto;
    }

    h2 {
        text-align: center;
        font-size: 24px;
    }

    p {
        text-align: center;
        margin-top: 16px;
    }

    .actions {
        margin-top: 32px;
        display: flex;
        justify-content: flex-end;
    }

</style>
