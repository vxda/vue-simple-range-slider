<template>
    <div>
        <div @click="onBarClick"
             class="range-slider-component">

            <div class="bar"
                 ref="bar">
                <span class="min">{{min}}</span>
                <template v-if="isStepsVisible">
                    <template v-for="step in numberOfSteps">
                        <span :class="{'is-selected': handlePosition > step}" :key="step" :style="{left: `${step}%`}"
                              class="checkpoint">|</span>
                    </template>
                </template>
                <button
                        :style="handleStyles"
                        @mousedown="onMouseDownHandler"
                        class="handle"
                        ref="handle"
                        type="button">
                </button>
                <span class="max">{{max}}</span>
                <div :style="progressBarStyles" class="progress-bar"></div>
            </div>

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
            default: Number,
            isStepsVisible: {
                type: Boolean,
                default: false
            },
            type: {
                type: String,
                default: 'step'
            }
        },
        data() {
            return {
                steps: this.getStepsNumber(),
                isDragging: false,
                onMouseDownPosition: 0,
                onMouseUpPosition: 0,
                handlePosition: this.default ? this.getDefault() : 0,
                barWidth: 0,
                isOutOfBounds: false
            }
        },
        methods: {
            onBarClick(e) {
                if (!this.isDragging) {
                    const position = ((e.clientX - this.$el.offsetLeft) / this.barWidth) * 100;

                    this.setHandlePosition(position);
                }
            },
            setHandlePosition(position) {
                this.handlePosition = this.handlePosition = this.numberOfSteps.sort((a, b) => {
                    return Math.abs(a - position) - Math.abs(b - position);
                })[0];
            },
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

                const position = ((e.clientX - this.onMouseDownPosition) / this.barWidth) * 100;

                if (this.type === 'smooth') {
                    //smooth
                    this.handlePosition = this.validateForBoundaries(position);
                } else {
                    // step
                    this.setHandlePosition(position);
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
                const result = [];
                let i;

                for (i = 0; i < this.steps + 1; i++) {
                    result.push(i / this.steps * 100);
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
            z-index: 1;
        }

        .progress-bar {
            width: 0;
            background-color: green;
            z-index: 2;
        }

        .handle {
            position: absolute;
            top: 50%;
            transform: translate(-50%, -50%);
            width: 20px;
            height: 20px;
            z-index: 10;
            overflow: hidden;
            backface-visibility: hidden;
            margin: 0;
        }

        .checkpoint {
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            user-select: none;
            display: block;
            white-space: nowrap;
            font-size: 0;
            color: transparent;
            width: 2px;
            height: 15px;
            background-color: #ccc;

            &.is-selected {
                background-color: green;
            }
        }

        .result {
            text-align: center;
        }
    }
</style>