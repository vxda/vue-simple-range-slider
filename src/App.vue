<template>
    <div id="app" @mousemove="onMouseMoveHandler" @mouseup="onMouseUpHandler">
        <h2>this is my slider</h2>
        <div class="wrapper">
            <range-slider-component
                ref="rangeSlider"
                :min="0"
                :max="6"
                :step="2"
            ></range-slider-component>
        </div>
    </div>
</template>

<script>
	import RangeSliderComponent from './components/RangeSliderComponent';

	export default {
		name: 'app',
		components: {
			RangeSliderComponent
		},
		methods: {
			onMouseMoveHandler(e) {
				this.$refs.rangeSlider.onMouseMoveHandler(e);
			},
			onMouseUpHandler(e) {
				this.$refs.rangeSlider.onMouseUpHandler(e);
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
