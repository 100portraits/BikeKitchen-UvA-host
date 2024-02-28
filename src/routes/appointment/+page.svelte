<div class='flex h-screen m-auto w-screen justify-center items-center'>
    <div class='h-full w-full flex flex-col'>
        <div class="bg-red-100 text-black p-5 text-4xl flex justify-center items-center">
            {#if step === 0}
                appointment type
            {:else if step === 1}
                repair type
            {:else if step === 2}
                mechanic + notes
            {:else if step === 3}
                price + confirm

            {/if}

            {#if step>0}
                <button class="absolute left-5  text-3xl" on:click={stepBack}>&#60;<span class="hover:underline decoration-2">back</span></button>
            {/if}
            {#if step === 0}
                <button class="absolute right-5  text-3xl" ><a class="hover:underline decoration-2" href="/dashboard">Dashboard</a>&#62;</button>
            {/if}
        </div>
        <div class="h-full text-4xl w-full">
        {#if step === 0}
            <div class="flex bg-red-300 h-full w-full justify-between gap-5">
                <button class="bg-red-100 ml-5 my-5 w-full flex justify-center items-center   hover:bg-red-50" on:click={() => setType("Scheduled")}>Scheduled</button>
                <button class="bg-red-100 mr-5 my-5 w-full flex justify-center items-center   hover:bg-red-50" on:click={() => setType("Drop-in")}>Drop-in</button>
            </div>

        {:else if step === 1}
            <div class="bg-red-300 w-full h-full flex justify-center items-center text-4xl  p-5">
                <div class="repair-type-buttons h-full w-full justify-between grid grid-cols-3 gap-x-5 gap-y-5">
                    <button class="bg-red-100 p-5 flex justify-center items-center hover:bg-red-50" on:click={() => setRepair("brakes")}>brakes</button>
                    <button class="bg-red-100 p-5 flex justify-center items-center hover:bg-red-50" on:click={() => setRepair("gears")}>gears</button>
                    <button class="bg-red-100 p-5 flex justify-center items-center hover:bg-red-50" on:click={() => setRepair("flat tire")}>flat tire</button>
                    <button class="bg-red-100 p-5 flex justify-center items-center hover:bg-red-50" on:click={() => setRepair("chain")}>chain</button>
                </div>
            </div>
        {:else if step === 2}
            <div class="bg-red-300 w-full h-full flex justify-center items-center text-3xl p-5">
                <div class="bg-red-100 w-full h-full flex py-10">
                    <form class="w-sm mx-auto  h-full flex flex-col gap-10">
                        <div>
                            <input class="bg-red-50 p-5" type="datetime-local" placeholder="datetime" bind:value={data.datetime} />
                            <button class="bg-red-200 p-5" on:click={() => data.datetime = new Date().toISOString().slice(0, 16)}>Now</button>
                        </div>

                        <input class="bg-red-50 p-5" type="text" placeholder="mechanic" bind:value={data.mechanic} />
                        <textarea class="bg-red-50 p-5" placeholder="notes" bind:value={data.notes}></textarea>
                        <button class="bg-red-200 p-5" on:click={stepForward}>next</button>
                    </form>
                </div>
            </div>

        {:else if step === 3}
            <div class="bg-red-300 w-full h-full flex justify-center items-center text-3xl p-5">
                <div class="bg-red-100 w-full h-full flex py-10">
                    <form class="w-sm mx-auto  h-full flex flex-col gap-10">
                        <input class="bg-red-50 p-5" type="number" placeholder="price" bind:value={data.price} />
                        <button class="bg-red-200 p-5" on:click={stepForward}>confirm</button>
                    </form>
                </div>
            </div>

        {/if}
        </div>
    </div>
</div>

<script>
    import { onMount } from "svelte";
    
    
    let step = 0;
    
    function stepBack() {
        step = step - 1;
    }

    function stepForward() {
        step = step + 1;
        if (step === 4) {
            //generate random ID
            data.id = Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);

            //console log the data as a json
            console.log(JSON.stringify(data));
            step = 0;
        }
    }


    let data = {
        id: "",
        type: "",
        repair: "",
        datetime: "",
        mechanic: "",
        notes: "",
        price: "",
    }

    
    function setType(type) {
        data.type = type;
        stepForward();
    }

    function setRepair(repair) {
        data.repair = repair;
        stepForward();
    }

</script>
                