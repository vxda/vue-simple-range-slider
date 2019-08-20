<template>
    <div>
        is dragging:{{isDragging}} - onMouseDownPosition: {{onMouseDownPosition}} - handlePosition: {{handlePosition}} -


        <div class="range-slider-component">

            <div class="bar"
                 ref="bar">
                <span class="min">{{min}}</span>
                <button
                    :style="handleStyles"
                    ref="handle"
                    class="handle"
                    type="button"
                    @mousedown="onMouseDownHandler"
                >
                    
                </button>
                <span class="max">{{max}}</span>
            </div>
            <div class="progress-bar" :style="progressBarStyles"></div>
        </div>
        <div class="result">
            currentValue: {{currentValue}}
        </div>
    </div>
</template>


<script>
	export default {
		name: 'RangeSliderComponent',
		props: {
			min: Number,
			max: Number,
			step: Number
		},
		data() {
			return {
				isDragging: false,
				onMouseDownPosition: 0,
				onMouseUpPosition: 0,
				handlePosition: 0,
				barWidth: 0,
				isOutOfBounds: false
			}
		},
		methods: {
			validateForBoundaries(value) {

				if (value >= this.max) {
					return this.max;
				} else if (value <= this.min) {
					return this.min;
				} else {
					return value;
				}
			},
			onMouseDownHandler(e) {
				this.isDragging = true;
				const {handle} = this.$refs;

				this.onMouseDownPosition = e.clientX - handle.offsetLeft
			},
			onMouseUpHandler() {
				this.isDragging = false;
			},
			onMouseMoveHandler(e) {
				if (!this.isDragging) {
					return;
				}

				this.handlePosition = this.validateForBoundaries(((e.clientX - this.onMouseDownPosition) / this.barWidth) * 100);
			}
		},
		computed: {
			handleStyles() {
				return {
					left: `${this.handlePosition}%`
				}
			},
			progressBarStyles() {
				return {
					width: `${this.handlePosition}%`
				}
			},
			currentValue() {
				const range = (this.max / this.barWidth) * 100;

				console.log('this.step', this.step);

				return ((this.handlePosition * this.step) / range)

			}
		},
		mounted() {
			this.barWidth = this.$refs.bar.getBoundingClientRect().width;
		}
	}
</script>

<style lang="scss">
    .range-slider-component {
        height: 30px;
        position: relative;

        .min {
            position: absolute;
            left: 0;
            top: 20px;
        }

        .max {
            position: absolute;
            right: 0;
            top: 20px;
        }

        .bar,
        .progress-bar {
            height: 2px;
            width: 100%;
            background-color: #ccc;
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
        }

        .progress-bar {
            width: 0;
            background-color: green;
        }

        .handle {
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            /*left: 0;*/
        }

        .result {
            text-align: center;
        }
    }
</style>