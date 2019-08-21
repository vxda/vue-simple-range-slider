<template>
    <div @mousemove="onMouseMoveHandler" @mouseup="onMouseUpHandler" id="app">
        <h2>this is my slider</h2>
        <div class="wrapper">
            <range-slider-component
                    :decimals="0"
                    :default="805"
                    :max="1000"
                    :min="800"
                    :step="5"
                    :is-steps-visible="true"
                    @onValueChange="onValChange"
                    ref="rangeSlider"
            ></range-slider-component>
        </div>

        VALUE EXTRACTED: {{rangeSliderValue}}
    </div>
</template>

<script>
    import RangeSliderComponent from './components/RangeSliderComponent';

    export default {
        name: 'app',
        data() {
            return {
                rangeSliderValue: 0
            }
        },
        components: {
            RangeSliderComponent
        },
        methods: {
            onMouseMoveHandler(e) {
                this.$refs.rangeSlider.onMouseMoveHandler(e);
            },
            onMouseUpHandler(e) {
                this.$refs.rangeSlider.onMouseUpHandler(e);
            },
            onValChange(val) {
                this.rangeSliderValue = val;
            }
        }
    }
</script>

<style lang="scss">
    body,
    html {
        height: 100%;
    }

    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
        height: 100%;

        .wrapper {
            width: 800px;
            margin-left: auto;
            margin-right: auto;
        }
    }
</style>
