<script>

    import {SvelteToast} from '@zerodevx/svelte-toast'
    import {closeModal, Modals} from "svelte-modals";
    import Navbar from "./lib/widgets/Navbar.svelte";
    import Home from "./lib/pages/Home.svelte";

</script>
<SvelteToast/>
<Modals>
    <div
            slot="backdrop"
            class="backdrop"
            on:click={closeModal}
    ></div>
</Modals>
<Navbar />

<Home />

<style>
    .backdrop {
        position: fixed;
        top: 0;
        bottom: 0;
        right: 0;
        left: 0;
        background: rgba(0, 0, 0, 0.50)
    }
</style>
