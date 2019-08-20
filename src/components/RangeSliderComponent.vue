<template>
    <div>
        is dragging:{{isDragging}} - onMouseDownPosition: {{onMouseDownPosition}} - handlePosition: {{handlePosition}} -
        <!--        numberOfSteps: {{numberOfSteps}}-->


        <div class="range-slider-component">

            <div class="bar"
                 ref="bar">
                <span class="min">{{min}}</span>
                <template v-for="step in numberOfSteps">
                    <span class="checkpoint" :style="step">|</span>
                </template>
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
				type: 'step',
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

				if (value >= 100) {
					return 100;
				} else if (value <= 0) {
					return 0;
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

				const pos = (e.clientX - this.onMouseDownPosition);

				console.log(pos);

				if (this.type === 'smooth') {
					//smooth
					this.handlePosition = this.validateForBoundaries(((e.clientX - this.onMouseDownPosition) / this.barWidth) * 100);
				} else {
					// step
					this.handlePosition = this.validateForBoundaries(((e.clientX - this.onMouseDownPosition) / this.barWidth) * 100);
				}

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

				return Math.round((this.max / 100) * this.handlePosition);

			},
			numberOfSteps() {
				const steps = this.max / this.step;
				const checkpoints = (this.barWidth * 100) / (this.barWidth / steps);
				const result = [];
				let i;

				console.log('steps', steps);

				for (i = 0; i < steps; i++) {
					console.log('ASDFASDF');
					result.push({left: `${checkpoints * i}%`});
				}

				console.log('result', result);

				return result;

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

        .checkpoint {
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
        }

        .result {
            text-align: center;
        }
    }
</style>