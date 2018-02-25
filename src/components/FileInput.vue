<template>
    <div class="file-container">
        <div :class="['drop-zone', {'drop-zone--hover': zoneHover}]">
            <div class="drop-zone__inner" @click.self="clickLabel"
                @dragover.prevent="zoneHover = true"
                @dragenter.prevent="zoneHover = true"
                @dragleave.prevent="zoneHover = false"
                @dragend.prevent="zoneHover = false"
                @drop.prevent="loadFile">
                <input id="file" ref="fileInput" class="drop-zone__input" type="file" @change="fileChanged">
                <label ref="fileLabel" class="drop-zone__label" for="file">{{ labelText }}</label>
            </div>
        </div>
        <div class="file-container__error">{{ error }}</div>
    </div>
</template>

<script>
export default {
    name: 'FileInput',
    props: {
        fileFormats: {
            type: Array,
            required: true,
        }
    },
    data() {
        return {
            zoneHover: false,
            file: null,
            error: null,
        } 
    },
    computed: {
        labelText() {
            if (null === this.file) {
                return 'Click or drop your .srt file here';
            }

            return this.file.name;
        }
    },
    methods: {
        loadFile(event) {
            this.zoneHover = false;
            this.$refs.fileInput.files = event.dataTransfer.files;
        },
        fileChanged(event) {
            if (event.target.files.length === 0) {
                this.file = null;
            } else {
                this.file = this.isValidFile(event.target.files[0]) ? event.target.files[0] : null;
            }

            this.$emit('change', this.file);
        },
        isValidFile(file) {
            this.error = null;

            if (!this.fileFormats.includes(file.name.split('.').pop())) {
                this.error = 'The file format is not supported';

                return false;
            }

            return true;
        },
        clickLabel() {
            this.$refs.fileLabel.click();
        }
    }
}
</script>

<style lang="scss" scoped>
.drop-zone {
    padding: 7px;
    margin-top: 40px;

    &, &__inner {
        background-color: #f9f9f9;
        border-radius: 5px;
    }

    &--hover, &--hover &__inner {
        background-color: #fff;
    }

    &__inner {
        border: 2px dashed #a2a2a2;
        padding: 60px 5px;
        cursor: pointer;
    }

    &__input {
        width: 0.1px;
        height: 0.1px;
        opacity: 0;
        overflow: hidden;
        position: absolute;
        z-index: -1;
    }

    &__label {
        cursor: pointer;
        font-size: 16px;
        color: #a39a9a;
    }
}

.file-container__error {
    color: #c50505;
    margin-top: 15px;
}
</style>