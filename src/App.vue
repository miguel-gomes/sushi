<template>
    <div class="container">
        <SubtitleShifter></SubtitleShifter>
    </div>
</template>

<script>
import SubtitleShifter from './components/SubtitleShifter.vue'

export default {
    name: 'app',
    components: {
        SubtitleShifter
    }
}
</script>

<style lang="scss">
body {
    background-color: #f1f1f1;
    font-family: 'Open sans', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #424242;
}

.container {
    max-width: 920px;
    margin: 0 auto;
}

.hide {
    display: none;
}

.button {
    background-color: #f1f1f1;
    color: #1d1d1d;
    padding: .5rem 1rem;
    font-weight: 400;
    border: 1px solid #bbb;
    border-radius: 2px;
    cursor: pointer;
    font-size: 1rem;
    line-height: 1.25;
    vertical-align: middle;
    text-align: center;
    text-decoration: none;

    &--success {
        color: #fff;
        background-color: #2992d0;
        border-color: transparent;
    }
}
</style>
