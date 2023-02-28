<script>

    import {SvelteToast} from '@zerodevx/svelte-toast'
    import {closeModal, Modals} from "svelte-modals";
    import Navbar from "./lib/widgets/Navbar.svelte";
    import Home from "./lib/pages/Home.svelte";
    import {userD} from "./utils/auth.js";
    import RegisterWidget from "./lib/modals/RegisterWidget.svelte";
    import LoginWidget from "./lib/modals/LoginWidget.svelte";
    import {Route, Router} from "svelte-navigator";
    import LeaderBoard from "./lib/pages/LeaderBoard.svelte";

</script>
<SvelteToast/>
<Modals>
    <div
            slot="backdrop"
            class="backdrop"
            on:click={closeModal}
    ></div>
</Modals>


<Router>
    <Navbar/>

    <Route primary={true} path="/">
        {#if $userD.token}
            <Home/>
        {:else}
            <div class="container-fluid">
                <div class="row">
                    <div class="col-lg-3"></div>
                    <div class="col-lg-3 col-md-6 col-sm-12">
                        <RegisterWidget/>
                    </div>
                    <div class="col-lg-3 col-md-6 col-sm-12">
                        <LoginWidget/>
                    </div>
                    <div class="col-lg-3"></div>
                </div>
            </div>
        {/if}
    </Route>
    <Route path="leaderboard" primary={false}>
        <LeaderBoard />
    </Route>
</Router>

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
