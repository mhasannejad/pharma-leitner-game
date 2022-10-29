<script>
    import LoginWidget from "./LoginWidget.svelte";
    import {closeModal} from "svelte-modals";

    export let isOpen
</script>
{#if isOpen}}
    <div role="dialog" class="modal">
        <div class="contents">
            <LoginWidget on:loggedin={()=>{closeModal()}}/>
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
        min-width: 450px;
        border-radius: 6px;
        padding: 0;
        background: rgba(0,0,0,0);
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        pointer-events: auto;
    }

    h2 {
        text-align: center;
        font-size: 24px;
        color: #212121;
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
