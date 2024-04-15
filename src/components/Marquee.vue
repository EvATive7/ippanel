<template>
    <div class="marquee" ref="marquee">
        <div class="marquee-content" :style="contentStyle" :class="{ 'fade-out': fadingOut }">
            <slot></slot>
        </div>
    </div>
</template>

<script>
export default {
    name: 'MarqueeText',
    props: {
        speed: {
            type: Number,
            default: 1 // Slow speed to provide smooth scrolling
        },
        startDelay: {
            type: Number,
            default: 3000 // Delay time before scrolling starts
        },
        endDelay: {
            type: Number,
            default: 3000 // Delay time for fading out after scrolling ends
        },
        fadeDuration: {
            type: Number,
            default: 300 // Duration of fade-out and fade-in effects
        }
    },
    data() {
        return {
            offset: 0,
            animationFrame: null,

            fadingOut: false,
            animating: false,

            lastSlotContent: null

        };
    },
    computed: {
        contentStyle() {
            return {
                transform: `translateX(-${this.offset}px)`
            }
        }
    },
    mounted() {
        //this.startMarquee()
    },
    beforeDestroy() {
        cancelAnimationFrame(this.animationFrame);
    },
    updated() {
        const newSlotContent = this.$slots.default()[0].children
        if (newSlotContent != this.lastSlotContent) {
            this.startMarquee()
            this.lastSlotContent = newSlotContent
        }
    },
    methods: {
        contentWidth() {
            return this.$refs.marquee.querySelector('.marquee-content').clientWidth
        },
        marqueeWidth() {
            return this.$refs.marquee.clientWidth
        },
        shouldScroll() {
            return this.contentWidth() > this.marqueeWidth();
        },
        endPosition() {
            return this.contentWidth() - this.marqueeWidth();
        },
        startMarquee() {
            setTimeout(() => {
                this.animating = true
                const animate = () => {
                    if (!this.shouldScroll()) {
                        // No need to roll, no more rolling
                        cancelAnimationFrame(this.animationFrame)
                        this.animating = false

                        return
                    }

                    if (this.endPosition() < this.offset) {
                        //One scroll ends normally, reset needed
                        cancelAnimationFrame(this.animationFrame)
                        this.animating = false

                        setTimeout(() => {
                            this.fadingOut = true
                            setTimeout(() => {
                                this.offset = 0
                                this.fadingOut = false
                                setTimeout(() => {
                                    this.startMarquee()
                                }, this.fadeDuration);
                            }, this.fadeDuration);
                        }, this.endDelay);
                        return
                    }

                    this.offset += this.speed;
                    this.animationFrame = requestAnimationFrame(animate)
                };
                animate()
            }, this.startDelay);
        },
    },
};
</script>

<style scoped>
.marquee {
    overflow: hidden;
    white-space: nowrap;
    width: 100%;
    position: relative;
}

.marquee-content {
    will-change: transform;
    display: inline-block;
    transition: opacity 0.3s ease-in-out;
}

.fade-out {
    opacity: 0;
}
</style>