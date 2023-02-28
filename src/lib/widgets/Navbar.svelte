<script>
    import {openModal} from "svelte-modals";
    import LoginModal from "../modals/LoginModal.svelte";
    import RegisterModal from "../modals/RegisterModal.svelte";
    import {userD} from "../../utils/auth.js";
    import {Link, navigate} from "svelte-navigator";
    import {NavItem} from "sveltestrap";
</script>

<nav class="navbar navbar-expand-lg bg-dark text-white mb-5"
     style="width: 100% !important;background-color: #212121 !important">
    <div class="container-fluid">
        <a class="navbar-brand text-white fw-light" on:click={()=>{navigate('/')}} >Pharma Leitner Game   |   </a>
        <a class="navbar-brand text-white fw-light my-2" on:click={()=>{navigate('/leaderboard')}}>Leader Board</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent"
                aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarSupportedContent">


        </div>
        <div class="d-flex justify-content-between ">
            {#if !$userD.token}
                <button class="btn btn-outline-success me-auto mx-2" type="submit" on:click={()=>{
                openModal(LoginModal)
            }}>Login
                </button>

                <button class="btn btn-outline-success me-auto mx-2" type="submit" on:click={()=>{
                openModal(RegisterModal)
            }}>Register
                </button>
            {:else}
                <button class="btn btn-outline-danger me-auto mx-2" type="submit" on:click={()=>{
                $userD = {}
            }}>Logout
                </button>
            {/if}
        </div>
    </div>
</nav>
