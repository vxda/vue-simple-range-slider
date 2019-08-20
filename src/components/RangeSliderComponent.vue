<template>
    <div>
        is dragging:{{isDragging}} - onMouseDownPosition: {{onMouseDownPosition}} - handlePosition: {{handlePosition}} -
        <!--        numberOfSteps: {{numberOfSteps}}-->


        <div class="range-slider-component">

            <div class="bar"
                 ref="bar">
                <span class="min">{{min}}</span>
                <!--                <template v-for="step in numberOfSteps">-->
                <!--                    <span :key="step" class="checkpoint" :style="{left: `${step}%`}">|</span>-->
                <!--                </template>-->
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
            step: Number,
            decimals: Number,
            default: Number
        },
        data() {
            return {
                type: 'step',
                isDragging: false,
                onMouseDownPosition: 0,
                onMouseUpPosition: 0,
                handlePosition: this.default ? this.getDefault() : 0,
                barWidth: 0,
                isOutOfBounds: false
            }
        },
        methods: {
            getStepsNumber() {
                return Math.ceil((this.max - this.min) / this.step);
            },
            getDefault() {
                if (this.default < this.min) {
                    return 0;
                } else if (this.default > this.max) {
                    return 100;
                } else {
                    return ((this.default - this.min) / this.step / this.getStepsNumber()) * 100;
                }

            },
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

                const pos = ((e.clientX - this.onMouseDownPosition) / this.barWidth) * 100;

                if (this.type === 'smooth') {
                    //smooth
                    console.log('ASDASDASD');
                    this.handlePosition = this.validateForBoundaries(pos);
                } else {
                    // step

                    this.handlePosition = this.numberOfSteps.sort((a, b) => {
                        return Math.abs(a - pos) - Math.abs(b - pos);
                    })[0];
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

                return (((this.max - this.min) / 100) * this.handlePosition + this.min).toFixed(this.decimals || 0);

            },
            numberOfSteps() {
                const steps = this.getStepsNumber();
                const result = [];
                let i;

                for (i = 0; i < steps + 1; i++) {
                    result.push(i / steps * 100);
                }

                return result.sort((a, b) => (a - b));

            }
        },
        mounted() {
            this.barWidth = this.$refs.bar.getBoundingClientRect().width;
        },
        watch: {
            currentValue: {
                immediate: false,
                handler(newValue) {
                    this.$emit('onValueChange', newValue);
                }
            }
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
            transform: translate(-50%, -50%);
            width: 20px;
            height: 20px;
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