<template>
    <div class="subtitle-shifter" ref="subtitleShifter">
        <h1>Online Subtitle Shifter</h1>
        <p class="description">
            This is a simple project made with Vue.js to shift .srt files : <br>
            simply drop a file, adjust the timings and download the new generated file !
        </p>
        <FileInput @change="loadFile" :fileFormats="['srt']"></FileInput>
        <div class="timings">
            <SwitchInput v-model="switchValue"></SwitchInput>
            <NumberInput :min="0" :max="99" v-model="h"></NumberInput>
            <span class="timings__separator">:</span>
            <NumberInput :min="0" :max="59" v-model="m"></NumberInput>
            <span class="timings__separator">:</span>
            <NumberInput :min="0" :max="59" v-model="s"></NumberInput>
            <span class="timings__separator">.</span>
            <NumberInput :large="true" :min="0" :max="999" v-model="ms"></NumberInput>
        </div>
        <div class="button-container">
            <a class="button button--success"
                v-if="file !== null"
                :href="subtitleUrl"
                :download="file.name"
            >
                Download subtitles
            </a>
        </div>
    </div>
</template>

<script>
import SwitchInput from './SwitchInput.vue';
import NumberInput from './NumberInput.vue';
import FileInput from './FileInput.vue';

export default {
    name: 'SubtitleShifter',
    components: {
        SwitchInput,
        NumberInput,
        FileInput,
    },
    data() {
        return {
            fileReader: new FileReader(),
            fileLines: [],
            h: 0,
            m: 0,
            s: 0,
            ms: 0,
            switchValue: true,
            file: null,
        };
    },
    computed: {
        sub() {
            return !this.switchValue;
        },
        subtitleUrl() {
            return window.URL.createObjectURL(this.subtitleBlob);
        },
        subtitleBlob() {
            return new Blob(["\ufeff", this.shiftSubtitleLines(this.fileLines, this.h, this.m, this.s, this.ms, this.sub)]);
        }
    },
    mounted() {
        this.fileReader.onload = () => {
            this.fileLines = this.fileReader.result.split("\n");
        };
    },
    methods: {
        pad(num, size) {
            let s = num + '';
            while (s.length < size) s = '0' + s;
            return s;
        },
        convertToMilliseconds(hours, minutes, seconds, milliseconds) {
            return parseInt(milliseconds + (1000 * seconds) + (60000 * minutes) + (3600000 * hours));
        },
        convertToHMSMs(milliseconds) {
            let hours = Math.floor(milliseconds / 3600000);
            milliseconds = milliseconds % 3600000;

            let minutes = Math.floor(milliseconds / 60000);
            milliseconds = milliseconds % 60000;

            let seconds = Math.floor(milliseconds / 1000);
            milliseconds = milliseconds % 1000;

            return [
                this.pad(hours, 2), this.pad(minutes, 2), this.pad(seconds, 2), this.pad(milliseconds, 3)
            ]
        },
        shiftSubtitle(timeStart, timeEnd, shiftTime, sub) {
            if (sub) {
                timeStart -= shiftTime;
                timeEnd   -= shiftTime;
            } else {
                timeStart += shiftTime;
                timeEnd   += shiftTime;
            }

            timeStart = Math.max(timeStart, 0);
            timeEnd   = Math.max(timeEnd, 0);

            return this.convertToHMSMs(timeStart).concat(this.convertToHMSMs(timeEnd));
        },
        shiftSubtitleLines(lines, h, m, s, ms, sub) {
            let newContent = '';

            for (let i = 0; i < lines.length; i++) {
                let result = lines[i].match(/(\d+):(\d+):(\d+),(\d+) --> (\d+):(\d+):(\d+),(\d+)/)

                if (result) {
                    let timeStart = this.convertToMilliseconds(parseInt(result[1]), parseInt(result[2]), parseInt(result[3]), parseInt(result[4])),
                        timeEnd = this.convertToMilliseconds(parseInt(result[5]), parseInt(result[6]), parseInt(result[7]), parseInt(result[8])),
                        shiftTime = this.convertToMilliseconds(h, m, s, ms);

                    let shiftedValues = this.shiftSubtitle(timeStart, timeEnd, shiftTime, sub);

                    let newLine = shiftedValues[0] + ':' + shiftedValues[1] + ':' + shiftedValues[2] + ',' + shiftedValues[3] + ' --> ' + shiftedValues[4] + ':' + shiftedValues[5] + ':' + shiftedValues[6] + ',' + shiftedValues[7];

                    newContent += newLine + "\n";
                } else {
                    newContent += lines[i] + "\n";
                }
            }

            return newContent;
        },
        loadFile(file) {
            this.file = file;

            if (null !== file) {
                this.fileReader.readAsText(file);
            }
        },
    }
};
</script>

<style lang="scss" scoped>
.subtitle-shifter {
    text-align: center;
    max-width: 450px;
    margin: 0 auto;
    margin-top: 10%;
}

.description {
    font-size: 15px;
}

.timings {
    margin-top: 20px;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;

    &__separator {
        line-height: 47px;
        font-size: 30px;
    }
}

.button-container {
    margin-top: 40px;
}
</style>