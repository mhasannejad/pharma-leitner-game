<script>


    import axios from "axios";
    import {baseUrl} from "../../utils/consts.js";
    import {userD} from "../../utils/auth.js";
    import {onMount} from "svelte";

    let levels = []
    let pharma_to_remember = {}
    let main_pharma = {}
    let blur = true
    let props_blur_holder = []
    let refrence = {
        "clinicalAttentions": "نکات بالینی",
        "pregnancyCategory": "دسته مصرت در حاملگی",
        "tradeNames": "نام های تجاری",
        "contraindications": "کنترایندیکیشن",
        "pregnancy": "حاملگی",
        "treatmentCategory": "دسته درمانی",
        "iranianGenericProducts": "محصولات ژنریک ایرانی",
        "indications": "اندیکاسیون",
        "pharmacoDynamics": "فارماکودینامیک",
        "interactions": "تداخلات",
        "sideEffects": "عوارض",
        "breastFeeding": "در شیردهی",
        "trainings": "آموزشات لازم",
        'block_combined': "در این بلاک ها هست"
    }


    onMount(() => {
        getPharmaToRemember(0)
        getMyLevels()
    })

    const getMyLevels = () => {
        axios({
            url: `${baseUrl}leitner/flashcard/mine/`,
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
            url: `${baseUrl}leitner/daroo/random/`,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer ' + $userD.token
            }, data: JSON.stringify({
                level: level
            })
        }).then(r => {
            console.log(r.data)
            pharma_to_remember = r.data
            main_pharma = {...r.data}
            delete pharma_to_remember.id
            delete pharma_to_remember.name
            delete pharma_to_remember.pharmacologyCategory
            console.log(main_pharma)
            console.log(pharma_to_remember)
            props_blur_holder = []
            let index_ph = 0
            for (let [key, value] of Object.entries(pharma_to_remember)) {
                console.log(key)
                console.log(value)
                props_blur_holder = [...props_blur_holder, {
                    blurred: true,
                    id: index_ph
                }]
                index_ph += 1
            }
            // pharma_to_remember.props.forEach(prop => {
            //
            //     props_blur_holder = [...props_blur_holder, {
            //         blurred: true,
            //         id: prop.id
            //     }]
            // })
            console.log(props_blur_holder)


        })


    }
    const un_blurred_prop = (index) => {

        props_blur_holder[index] = {
            id: props_blur_holder[index].id, blurred: false
        }
        console.log(props_blur_holder)
    }
    const upgradeCard = (pharma, remembered) => {
        axios({
            url: `${baseUrl}leitner/flashcard/upgrade/`,
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

    function setBlock(block) {
        axios({
            url: `${baseUrl}leitner/block/set/priority/`,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer ' + $userD.token
            },
            data: JSON.stringify({
                block_priority: block,
            })
        }).then(r => {
            if (r.status === 200) {
                $userD = r.data
            }

        })
    }

    function isJsonString(str) {
        try {
            console.log('type of ff'+typeof str)
            JSON.parse(str);
        } catch (e) {
            console.log(str)
            console.log(e)
            return false;
        }
        return true;
    }
</script>


<div class="container">
    <h4>set your block priority</h4>
    <div class="w-100 d-flex justify-content-between">
        {#each [1, 2, 3, 4] as block}
            <button on:click={()=>{setBlock(block)}}
                    class="{$userD.block_priority===block?'active-block-button':''} m-1" style="width: 22%">
                block {block}
            </button>
        {/each}
    </div>
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
                    <h3 class="">{main_pharma.name}</h3>
                </div>
                <div class="card-body">
                    <h5 class="card-title" on:click={()=>{blur=false}}
                        class:blur-property={blur}>{isJsonString(main_pharma.pharmacologyCategory) ? JSON.parse(main_pharma.pharmacologyCategory) : main_pharma.pharmacologyCategory}</h5>

                    <div class="btn-group w-100 my-3" role="group" aria-label="Basic example">
                        <button type="button" class="btn btn-danger"
                                on:click={()=>{upgradeCard(main_pharma.id,false)}}>i can't remember this
                        </button>
                        <button type="button" class="btn btn-success"
                                on:click={()=>{upgradeCard(main_pharma.id,true)}}>i remember this
                        </button>
                    </div>
                    <div class="accordion my-3 bg-dark" id="accordionExample" style="direction: rtl !important">
                        {#if pharma_to_remember}
                            {#each Object.entries(pharma_to_remember) as [key, value],index}
                                <div class="accordion-item">
                                    <h2 class="accordion-header" id="heading{index}">
                                        <button class="accordion-button" type="button" data-bs-toggle="collapse"
                                                data-bs-target="#collapse{index}" aria-expanded="true"
                                                aria-controls="collapse{index}">
                                            {refrence[key]}
                                        </button>
                                    </h2>
                                    <div id="collapse{index}" class="accordion-collapse collapse show"
                                         aria-labelledby="heading{index}" data-bs-parent="#accordionExample">
                                        <div class="accordion-body "
                                             class:blur-property={props_blur_holder[index].blurred} on:click={()=>{
                                                 un_blurred_prop(index)
                                             }}>
                                            {#if isJsonString(value) && value != null}
                                                {#if Array.isArray(JSON.parse(value))}
                                                    <ul>
                                                        {#each JSON.parse(value) as item_}
                                                            <li>{item_}</li>
                                                        {/each}
                                                    </ul>
                                                {:else}
                                                    {#each Object.entries(JSON.parse(value)||{}) as [k1, v1],ind}
                                                        <h3> &ensp {k1}</h3>
                                                        {#if isJsonString(JSON.stringify(v1))}
                                                            {#if Array.isArray(v1)}
                                                                <ul>
                                                                    {#each v1 as item_2}
                                                                        <li> &ensp{item_2}</li>
                                                                    {/each}
                                                                </ul>
                                                            {:else}
                                                                {#each Object.entries(v1) as [k2, v2],ind}
                                                                    <h4> &ensp  &ensp {k2}</h4>
                                                                    {#if isJsonString(JSON.stringify(v2))}
                                                                        {#if Array.isArray(v2)}
                                                                            <ul>
                                                                                {#each v2 as item_2}
                                                                                    <li>  &ensp  &ensp {item_2}</li>
                                                                                {/each}
                                                                            </ul>
                                                                        {:else}
                                                                            {#each Object.entries(v2) as [k3, v3],ind}
                                                                                <h5> &ensp  &ensp  &ensp {k3}</h5>
                                                                                <p> &ensp  &ensp  &ensp{v3}</p>
                                                                            {/each}
                                                                        {/if}
                                                                    {:else}
                                                                        <p> &ensp  &ensp{v2}</p>
                                                                    {/if}
                                                                {/each}
                                                            {/if}
                                                        {:else}
                                                            <p> &ensp  &ensp{v1}</p>
                                                        {/if}
                                                    {/each}
                                                {/if}

                                            {:else}
                                                &ensp {value}
                                            {/if}
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
