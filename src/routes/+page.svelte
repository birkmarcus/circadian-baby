<script lang="ts">

    const enum STATE {
        NEW,
        RUNNING,
        PAUSED
    }

    let state: STATE = STATE.NEW;
    let startTime: number = 0;
    let elapsedTime: number = 0;
    let oldElapsedTime:number = 0;
    let interval: any;

    const pad2 = (number:number) => `00${number}`.slice(-2);
    const pad3 = (number:number) => `000${number}`.slice(-3);

    $: hours = pad2(Math.floor(elapsedTime / 1000 / 60 / 60 ) % 60);
    $: minutes = pad2(Math.floor(elapsedTime / 1000 / 60 ) % 60);
    $: secunds = pad2(Math.floor((elapsedTime / 1000)) % 60);
    $: millis = pad3(elapsedTime % 1000);
    $: formattetElapsedTime = `${hours}:${minutes}:${secunds}.${millis}`

    const start = () =>{
        startTime = Date.now();
        state = STATE.RUNNING;
        interval = setInterval(()=>{
            if(state === STATE.RUNNING){
                let endTime = Date.now()
                elapsedTime = endTime - startTime + oldElapsedTime;
            }
        });
    }
    const reset = () =>{
        elapsedTime = 0;
        state = STATE.NEW;
        clearInterval(interval);
    }

    const pause = () =>{
        state = STATE.PAUSED;
        oldElapsedTime = elapsedTime;
    }

    const resume = () =>{
        startTime = Date.now();
        state = STATE.RUNNING;
    }
</script>


<div class="h-screen bg-gradient-to-br from-blue-500 to-green-400
            flex flex-col justify-center items-center">
    <div class="glass w-2/3 h-36 p-4"> 

        <h1 class="mb-2 text-2xl text-yellow-100 border-b border-yellow-100">Cicadian Baby</h1>

        <p class="mb-2 text-xl text-white">Elapsede time: {formattetElapsedTime}</p>
        <div class="text-right">
            
            {#if state === STATE.NEW}
                <button on:click={start}
                class="border border-green-200 rounded text-green-200 text-xl px-2 mr-2">
                    Start</button>
            {/if}
            
            {#if state === STATE.RUNNING || state === STATE.PAUSED}
                <button on:click={reset}
                class="border border-red-500 rounded text-red-500 text-xl px-2 mr-2">
                    Reset</button>
            {/if}

            {#if state === STATE.RUNNING}
                <button on:click={pause} class="border border-white rounded text-white text-xl px-2 mr-2">
                    Pause</button>
            {/if}
            
            {#if state === STATE.PAUSED}
                <button on:click={resume} class="border border-black rounded text-black text-xl px-2 mr-2">
                    Resume</button>
            {/if}
        </div>
    </div>
</div>



<style>
    .glass{
        background-color: rgba(255, 235, 233, 0.15);
        box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
        backdrop-filter: blur(5px);
        border-radius: 10px;
    }
</style>