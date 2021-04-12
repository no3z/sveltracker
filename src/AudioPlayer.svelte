<script context="module">
    const players = new Set();

    export function playAll() {
        players.forEach((p) => p[0]());
    }

    export function stopAll() {
        players.forEach((p) => p[1]());
    }

    export function muteAll() {
        players.forEach((p) => p[2]());
    }
</script>

<script>
    import { onMount } from "svelte";
    import audioBufferSlice from "audiobuffer-slice";

    export let src;
    
    let actx;
    let bpm_time;
    let srcNode;
    let gainNode;
    let srcFile;
    let audioData;
    
    function play() {        
       // playLoop(audioData);
        srcNode.start(0, bpm_time);
        console.log("start", srcNode, bpm_time);
    }
    function stop() {
        if (srcNode) {
            srcNode.stop(0);
            srcNode.currentTime = 0;
        }
        console.log("stop", srcNode, bpm_time);
    }
    function mute() {
        if (gainNode) {
            if (gainNode.gain.value > 0) gainNode.gain.value = 0;
            else gainNode.gain.value = 1;   
            this.innerText =  gainNode.gain.value === 0 ? 'unmute' : 'mute';      
        }
        console.log("gain", gainNode.gain.value, bpm_time);
    }

    // Sets up a new source node as needed as stopping will render current invalid
    function playLoop(abuffer) {
    
        audioBufferSlice(
            abuffer,
            0,
            bpm_time,
            function (error, slicedAudioBuffer) {
                if (error) {
                    console.error(error);
                } else {
                                      
                    srcNode = actx.createBufferSource(); // create audio source
                    srcNode.duration = bpm_time;
                    srcNode.buffer = slicedAudioBuffer;
                    if (!audioData) audioData = slicedAudioBuffer; // create a reference for control buttons

                    //srcNode.buffer = abuffer;             // use decoded buffer
                    srcNode.connect(gainNode);
                    gainNode.connect(actx.destination);
                    //srcNode.connect(actx.destination); // create output
                    srcNode.loopEnd = bpm_time;
                    srcNode.loop = true; // takes care of perfect looping
                    //                // play...
                    console.log("LOADED", bpm_time, srcNode);
                    players.add([play, stop, mute]);
                }
            }
        );
        console.log("mothefucks11");
    }

    // Decode the audio file, then start the show
    function decode(buffer) {
        actx.decodeAudioData(buffer, playLoop);
    }

    onMount(() => {
        // Like players.push(player)
        bpm_time = (60000.0 / 96.0) * 16.0;

        actx = new (AudioContext || webkitAudioContext)(), audioData;

        gainNode = actx.createGain();

        // Load some audio (CORS need to be allowed or we won't be able to decode the data)
        fetch(src, { mode: "cors" })
            .then(function (resp) {
                return resp.arrayBuffer();
            })
            .then(decode);

        console.log("onMount videoplayer", players.size, src);
        srcFile = src;
    });
</script>

<div>    
	<button on:click={play}>play all!</button>
	<button on:click={stop}>Stop all!</button>
    <button on:click={mute}> mute </button>    {srcFile}
</div>
