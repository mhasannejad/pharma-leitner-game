<script>


    import axios from "axios";
    import {baseUrl} from "../../utils/consts.js";
    import {userD} from "../../utils/auth.js";
    import {onMount} from "svelte";

    let levels = []
    let pharma_to_remember = {}
    let blur = true
    let props_blur_holder = []

    onMount(() => {
        getPharmaToRemember(0)
        getMyLevels()
    })

    const getMyLevels = () => {
        axios({
            url: `${baseUrl}leitner/card/mine/`,
            method: 'GET',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer ' + $userD.token
            }
        }).then(r => {
            levels = r.data
        })
    }

    const getPharmaToRemember = (level) => {
        axios({
            url: `${baseUrl}leitner/pharma/random/`,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer ' + $userD.token
            }, data: JSON.stringify({
                level: level
            })
        }).then(r => {
            pharma_to_remember = r.data
            pharma_to_remember.props.forEach(prop => {
                props_blur_holder = [...props_blur_holder, {
                    blurred: true,
                    id: prop.id
                }]
            })


        })


    }
    const un_blurred_prop = (index) => {

        props_blur_holder[index] = {
            id:props_blur_holder[index].id, blurred: false
        }
    }
    const upgradeCard = (pharma, remembered) => {
        axios({
            url: `${baseUrl}leitner/card/upgrade/`,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer ' + $userD.token
            },
            data: JSON.stringify({
                is_remembered: remembered,
                pharma_id: pharma
            })
        }).then(r => {
            if (r.status === 200) {
                getMyLevels()
                getPharmaToRemember(0)
            }

        })
    }
</script>


<div class="container">
    <div class="d-flex justify-content-start w-100 mx-4 my-4">
        {#each levels as level}
            <button class="mx-2" on:click={()=>{
                getPharmaToRemember(level.level)
            }}>level {level.level} | {level.cards.length} cards
            </button>
        {/each}
    </div>
    <div class="d-flex justify-content-center h-100">
        <div class="w-75 justify-content-center h-100">
            <div class="card" style="">
                <div class="card-header">
                    <h3 class="">{pharma_to_remember.name}</h3>
                </div>
                <div class="card-body">
                    <h5 class="card-title" on:click={()=>{blur=false}}
                        class:blur-property={blur}>{pharma_to_remember.group}</h5>

                    <div class="btn-group w-100 my-3" role="group" aria-label="Basic example">
                        <button type="button" class="btn btn-danger"
                                on:click={()=>{upgradeCard(pharma_to_remember.id,false)}}>WTF is This
                        </button>
                        <button type="button" class="btn btn-success"
                                on:click={()=>{upgradeCard(pharma_to_remember.id,true)}}>i remember this
                        </button>
                    </div>
                    <div class="accordion my-3 bg-dark" id="accordionExample" style="direction: rtl !important">
                        {#if pharma_to_remember.props}
                            {#each pharma_to_remember.props as prop,index}
                                <div class="accordion-item">
                                    <h2 class="accordion-header" id="heading{index}">
                                        <button class="accordion-button" type="button" data-bs-toggle="collapse"
                                                data-bs-target="#collapse{index}" aria-expanded="true"
                                                aria-controls="collapse{index}">
                                            {prop.key}
                                        </button>
                                    </h2>
                                    <div id="collapse{index}" class="accordion-collapse collapse show"
                                         aria-labelledby="heading{index}" data-bs-parent="#accordionExample">
                                        <div class="accordion-body "
                                             class:blur-property={props_blur_holder[index].blurred} on:click={()=>{
                                                 un_blurred_prop(index)
                                             }}>
                                            {prop.value}
                                        </div>
                                    </div>
                                </div>
                            {/each}
                        {/if}

                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
