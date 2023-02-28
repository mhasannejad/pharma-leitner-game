<script>
    import {onMount} from "svelte";
    import axios from "axios";
    import {baseUrl} from "../../utils/consts.js";
    import {userD} from "../../utils/auth.js";

    let users = []

    onMount(() => {
        getUsers()
    })

    function getUsers() {
        axios({
            url: `${baseUrl}leitner/leaderboard/`,
            method: 'GET',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer ' + $userD.token
            }
        }).then(r => {
            users = r.data
            console.log(users)
        })
    }
</script>

<div class="container">
    <div class="row">
        <div class="col-4"></div>
        <div class="col-4">
            <h1>LeaderBoard</h1>
            {#each users as user}
                <div class="card my-5">
                    <div class="card-body">
                        {user.email} with {user.leitner_points} points
                    </div>
                </div>
            {/each}
        </div>
        <div class="col-4"></div>
    </div>
</div>
