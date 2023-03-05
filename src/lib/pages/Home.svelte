<script>


    import axios from "axios";
    import {baseUrl} from "../../utils/consts.js";
    import {userD} from "../../utils/auth.js";
    import {onMount} from "svelte";
    import {closeAllModals, openModal} from "svelte-modals";
    import loading from "../modals/loading.svelte";

    let loaded = false
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
        console.log('heeeerrrr')
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
        openModal(loading,{})

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
            closeAllModals()
            loaded = true;
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
        if (str == 'null') {
            return false;
        }
        try {
            console.log('str: ' + str)
            console.log('type of ff' + typeof str)
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
        {#if Object.keys(main_pharma).length > 0}
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
                        {#if isJsonString(main_pharma.tradeNames)}
                            <div class="card">
                                <div class="card-title">
                                    نام های تجاری
                                </div>
                                <div class="card-body" style="direction: ltr">
                                    <p>
                                        {JSON.parse(main_pharma.tradeNames).join(' , ')}
                                    </p>
                                </div>
                            </div>
                        {/if}
                        <div class="card">
                            <div class="card-title">
                                فراورده های ژنریک ایرانی
                            </div>
                            <div class="card-body" style="direction: ltr!important;text-align: left">
                                <ul>
                                    {#each JSON.parse(main_pharma.iranianGenericProducts) as item}
                                        <li>{item}</li>
                                    {/each}

                                </ul>
                            </div>
                        </div>
                        <div class="card">
                            <div class="card-title">
                                طبقه بندی درمانی
                            </div>
                            <div class="card-body" style="direction: ltr">
                                <p>
                                    {JSON.parse(main_pharma.treatmentCategory).join(' , ')}
                                </p>
                            </div>
                        </div>
                        <div class="card">
                            <div class="card-title">
                                طبقه بندی حاملگی
                            </div>
                            <div class="card-body" style="direction: ltr">
                                <p>
                                    {JSON.parse(main_pharma.pregnancyCategory)}
                                </p>
                            </div>
                        </div>
                        <div class="card">
                            <div class="card-title">
                                اندیکاسیون و دوزاژ
                            </div>
                            <div class="card-body"
                                 style="direction: rtl !important;align-content: start;text-align: right">
                                <ul>
                                    {#each Object.entries(JSON.parse(main_pharma.indications)) as [k, v],ind}
                                        <li>
                                            <div>
                                                <h3>
                                                    &emsp; &emsp; {k}&emsp; &emsp;
                                                </h3>
                                                <ul>
                                                    {#each Object.entries(v) as [k2, v2],ind2}
                                                        <li>
                                                            <div>
                                                                <h4>&emsp; &emsp; &emsp; &emsp;{k2}&emsp; &emsp; &emsp;
                                                                    &emsp;</h4>
                                                                <p>&emsp; &emsp; &emsp; &emsp;{v2.order}&emsp; &emsp;
                                                                    &emsp; &emsp;</p>
                                                            </div>
                                                        </li>
                                                    {/each}
                                                </ul>
                                            </div>
                                        </li>
                                    {/each}
                                </ul>
                            </div>
                        </div>
                        {#if isJsonString(main_pharma.pharmacoDynamics)}

                            <div class="card">
                                <div class="card-title">
                                    فارماکودینامیک و فارماکوکینتیک
                                </div>
                                <div class="card-body"
                                     style="direction: rtl !important;align-content: start;text-align: right">
                                    <h2>مکانیسم های اثر</h2>
                                    {#each Object.entries(JSON.parse(main_pharma.pharmacoDynamics).mechanism) as [k, v],ind}
                                        <h3>
                                            &emsp; &emsp; {k}&emsp; &emsp;
                                        </h3>
                                        <p>{v}</p>
                                    {/each}
                                    <h3>نیمه عمر: {JSON.parse(main_pharma.pharmacoDynamics).halfLife}</h3>
                                    <h3>دفع: {JSON.parse(main_pharma.pharmacoDynamics).excretion}</h3>
                                    {#if 'tables' in JSON.parse(main_pharma.pharmacoDynamics)}
                                        {#each JSON.parse(main_pharma.pharmacoDynamics).tables as table}
                                            <table class="table table-dark">
                                                <thead>
                                                <tr>
                                                    {#each table.header as head}
                                                        <th scope="col">{head}</th>
                                                    {/each}

                                                </tr>
                                                </thead>
                                                {#each table.content as content}
                                                    <tr>
                                                        {#each content as data}
                                                            <td>{data}</td>
                                                        {/each}
                                                    </tr>
                                                {/each}
                                            </table>
                                        {/each}
                                    {/if}
                                </div>
                            </div>
                        {/if}
                        {#if isJsonString(main_pharma.sideEffects)}
                            <div class="card">
                                <div class="card-title">
                                    عوارض جانبی
                                </div>
                                <div class="card-body"
                                     style="direction: rtl !important;align-content: start;text-align: right">
                                    {#each Object.entries(JSON.parse(main_pharma.sideEffects)) as [k, v],index}
                                        <h3>{k}</h3>
                                        <ul>
                                            {#each v as se}
                                                <li>{se}</li>
                                            {/each}
                                        </ul>
                                    {/each}
                                </div>
                            </div>
                        {/if}
                        {#if isJsonString(main_pharma.clinicalAttentions)}
                            <div class="card">
                                <div class="card-title">
                                    عوارض جانبی
                                </div>
                                <div class="card-body"
                                     style="direction: rtl !important;align-content: start;text-align: right">
                                    {#each Object.entries(JSON.parse(main_pharma.clinicalAttentions)) as [k, v],index}
                                        <h3>{k}</h3>
                                        <ul>
                                            {#each v as se}
                                                <li>{se}</li>
                                            {/each}
                                        </ul>
                                    {/each}
                                </div>
                            </div>
                        {/if}
                        {#if isJsonString(main_pharma.trainings)}
                            <div class="card">
                                <div class="card-title">
                                    آموزش های لازم
                                </div>
                                <div class="card-body" style="direction: rtl!important;text-align: right">
                                    <ul>
                                        {#each JSON.parse(main_pharma.trainings) as item}
                                            <li>{item}</li>
                                        {/each}

                                    </ul>
                                </div>
                            </div>
                        {/if}
                        {#if isJsonString(main_pharma.breastFeeding)}
                            <div class="card">
                                <div class="card-title">
                                    در شیر دهی
                                </div>
                                <div class="card-body" style="direction: rtl!important;text-align: right">
                                    <p>{JSON.parse(main_pharma.breastFeeding)}</p>
                                </div>
                            </div>
                        {/if}
                        <!--<div class="accordion my-3 bg-dark" id="accordionExample" style="direction: rtl !important">
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
                                                        {#each Object.entries(JSON.parse(value) || {}) as [k1, v1],ind}
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
                                                                        <h4> &ensp &ensp {k2}</h4>
                                                                        {#if isJsonString(JSON.stringify(v2))}
                                                                            {#if Array.isArray(v2)}
                                                                                <ul>
                                                                                    {#each v2 as item_2}
                                                                                        <li> &ensp &ensp {item_2}</li>
                                                                                    {/each}
                                                                                </ul>
                                                                            {:else}
                                                                                {#each Object.entries(v2) as [k3, v3],ind}
                                                                                    <h5> &ensp &ensp &ensp {k3}</h5>
                                                                                    <p> &ensp &ensp &ensp{v3}</p>
                                                                                {/each}
                                                                            {/if}
                                                                        {:else}
                                                                            <p> &ensp &ensp{v2}</p>
                                                                        {/if}
                                                                    {/each}
                                                                {/if}
                                                            {:else}
                                                                <p> &ensp &ensp{v1}</p>
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

                        </div>-->
                    </div>
                </div>
            </div>
        {/if}
    </div>
</div>
