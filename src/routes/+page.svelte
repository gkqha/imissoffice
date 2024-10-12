<script>
    import background from '../audio/748189__blaukreuz__220207_apartmentwindowopen_morning.mp3'; // Replace with your audio file paths
    import stapler from '../audio/85944__mwmarsh__stapler.mp3';
    import phone
        from '../audio/277597__coral_island_studios__adult-woman-phone-conversation-laughing-yeah.mp3';
    import fan from '../audio/347299__suhral__opening-and-closing-a-small-electric-fan.mp3';
    import whistle from '../audio/366874__tianve8__whistle.wav';
    import footsteps from '../audio/336598__inspectorj__footsteps-concrete-a.wav';
    import squeaking from '../audio/85941__mwmarsh__squeaking-wood-floor.mp3';
    import creaking from '../audio/346156__suhral__creaking-door_5.mp3';
    import beeping from '../audio/347300__suhral__scanner-beeping-sound.mp3';
    import car from '../audio/437502__tianve8__car-door.wav';
    import folding from '../audio/459442__coral_island_studios__11-carboard-box-folding.mp3';
    import cd from '../audio/484659__inspectorj__cd-player-turn-on-turn-off-01-01.wav';
    import typing from '../audio/380170__pfranzen__typing-gently-but-quickly.ogg';
    import banging from '../audio/583793__pfranzen__banging-a-table.ogg';
    import texting from '../audio/635220__pfranzen__texting-on-an-old-flip-phone.ogg';
    import leaving from '../audio/659703__pfranzen__small-group-of-people-leaving-a-room.ogg';
    import buzz from '../audio/223183__framing_noise__cellphonebuzzvibratedoublefasthand.wav';
    import typing2 from '../audio/624499__tinyray__typing-tiny.m4a';
    import typing3 from '../audio/685984__zrrion__keyboard-typing-sounds-modded-omnikey-101.mp3';
    import what from '../audio/685081__pfranzen__confused-man-saying-what.ogg';
    import click from '../audio/687106__aphom000__mouse-1-button-long-click.wav';
    import click2 from '../audio/678248__pixeliota__mouse-click-sound.mp3';
    import {onMount} from "svelte";
    import {Button} from "$lib/components/ui/button";
    import backgroundImg from "../img/olena-bohovyk-dIMJWLx1YbE-unsplash.jpg";
    import * as Select from "$lib/components/ui/select";

    const colleages = [
        {value: "5", label: "5"},
        {value: "6", label: "6"},
        {value: "7", label: "7"},
        {value: "8", label: "8"},
        {value: "9", label: "9"},
        {value: "10", label: "10"}
    ];

    let selectedNumber = {
        value: "5"
    };

    let audioCtx;
    let sourceBackground;
    let sourceOthers = [];
    let buffers = [];
    let isPlaying = false;
    let intervalId;

    onMount(async () => {
        audioCtx = new AudioContext();

        try {
            // Load background audio
            const backgroundAudioBuffer = await fetch(background).then(res => res.arrayBuffer());
            const backgroundBuffer = await audioCtx.decodeAudioData(backgroundAudioBuffer);
            buffers.push(backgroundBuffer)

            // Load other audio files
            const otherAudioBuffers = await Promise.all([stapler, phone, fan, whistle, footsteps, squeaking, creaking, beeping, car, folding, cd, typing, banging, texting, leaving, buzz, what, typing2, typing3, click, click2].map(async url => {
                const res = await fetch(url);
                return res.arrayBuffer();
            }));
            const otherBuffers = await Promise.all(otherAudioBuffers.map(buffer => audioCtx.decodeAudioData(buffer)));
            buffers = buffers.concat(otherBuffers)

        } catch (error) {
            console.error("Error loading audio:", error);
        }
    });

    function togglePlay() {
        isPlaying = !isPlaying;

        if (isPlaying) {
            playSound(buffers[0], true); // Play background audio

            let time = 6000 + (10 - Number(selectedNumber.value)) * 1500;

            intervalId = setInterval(() => {
                playRandomAudio()
            }, time);


        } else {
            stopSound(sourceBackground);
            clearInterval(intervalId);

            sourceOthers.forEach(source => {
                if (source) source.stop();
            });
            sourceOthers = []; // Clear the array after stopping
        }
    }


    function playSound(buffer, loop = false) {

        if (!buffer) return; // Guard
        const source = audioCtx.createBufferSource();
        source.buffer = buffer;
        source.loop = loop;
        source.connect(audioCtx.destination);

        if (loop) {
            sourceBackground = source
        }


        source.start();

        if (!loop) {
            sourceOthers.push(source)

            source.onended = () => {
                sourceOthers = sourceOthers.filter(s => s !== source);
            }
        }

    }


    function stopSound(source) {
        if (source) {
            source.stop();
            if (source === sourceBackground) sourceBackground = null;


        }
    }

    function playRandomAudio() {
        const otherBuffers = buffers.slice(1)


        if (!otherBuffers.length) return


        let randomIndex = Math.floor(Math.random() * otherBuffers.length);
        let randomBuffer = otherBuffers[randomIndex];

        // Stop currently playing other audio
        sourceOthers.forEach(source => source.stop());
        sourceOthers = []; // Clear the array

        playSound(randomBuffer);
    }


</script>

<div class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-lg flex-col items-center space-x-4">
    <div class="relative z-20 flex items-center text-lg font-medium">
        <!-- <Command class="mr-2 h-6 w-6" /> -->
        I miss office.
    </div>
    <div class="relative z-20 mt-auto">
        <blockquote class="space-y-2">
            <p class="text-sm">
                &ldquo;The hum of the fluorescent lights, a forgotten symphony. The gentle click-clack of keys, a
                rhythmic lullaby now lost to the silence of these walls. I miss the office, a place of quiet
                industry, a hive of collective purpose. I long for the hushed conversations over steaming mugs, the
                shared laughter echoing down sterile hallways, the camaraderie forged in deadlines met and projects
                conquered. I yearn for the familiar rhythm of the workday, the comforting predictability of a place
                where I belonged, a space that felt, in its own peculiar way, like home.&rdquo;
            </p>
        </blockquote>
    </div>
    <div>
        <div class="mx-auto flex w-full flex-col justify-center space-y-6">
            <p class="text-lg">How many colleages in the office?</p>
            <div class="flex flex-row">
                <Select.Root selected={selectedNumber}
                             onSelectedChange={(v) => {
                                   v && (selectedNumber.value = v.value);}}>
                    <Select.Trigger class="w-[180px]">
                        <Select.Value placeholder="number of colleages"/>
                    </Select.Trigger>
                    <Select.Content>
                        <Select.Group>
                            <Select.Label>colleages</Select.Label>
                            {#each colleages as colleage}
                                <Select.Item value={colleage.value} label={colleage.label}
                                >{colleage.label}</Select.Item
                                >
                            {/each}
                        </Select.Group>
                    </Select.Content>
                    <Select.Input name="favoriteFruit"/>
                </Select.Root>
                <div class="flex-auto ms-5"><p>{selectedNumber.value}</p></div>
            </div>
            <Button on:click={togglePlay}>
                {#if isPlaying}
                    Pause
                {:else}
                    Play
                {/if}
            </Button>
        </div>
    </div>
</div>
