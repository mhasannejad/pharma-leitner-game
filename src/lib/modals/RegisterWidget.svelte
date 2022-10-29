<script>
    import axios from "axios";

    import {createEventDispatcher} from "svelte";
    import {baseUrl} from "../../utils/consts.js";
    import {userD} from "../../utils/auth.js";

    let registerObj = {
        email: '',
        password: ''
    }

    let eventDispatcher = createEventDispatcher()
    const registerF = () => {
        axios({
            url: `${baseUrl}api/auth/register/`,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            data: JSON.stringify(registerObj)
        }).then(r => {
            if (r.status===200){
                $userD = r.data
                eventDispatcher('registered')
            }
        })
    }
</script>


<div class="card my-0 ">
    <div class="card-header text-dark">
        Register New Account
    </div>
    <div class="card-body">
        <div class="mb-3">
            <label class="form-label">Email address</label>
            <input bind:value={registerObj.email} type="email" class="form-control" id="email"
                   placeholder="name@example.com">
        </div>
        <div class="mb-3">
            <label class="form-label">Password</label>
            <input bind:value={registerObj.password} type="password" class="form-control"
                   placeholder="********">
        </div>
        <div class="mb-3">
            <label class="form-label">Retype Password</label>
            <input type="password" class="form-control" placeholder="********">
        </div>
        <button class="sm-btn w-100" on:click={registerF}>
            Register
        </button>
    </div>

</div>
