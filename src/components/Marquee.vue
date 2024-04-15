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
            default: 0.4 // 较慢的速度以提供平滑滚动
        },
        startDelay: {
            type: Number,
            default: 3000 // 滚动开始前的延迟时间
        },
        endDelay: {
            type: Number,
            default: 3000 // 滚动结束后等待淡出的延迟时间
        },
        fadeDuration: {
            type: Number,
            default: 300 // 淡出和淡入效果的持续时间
        }
    },
    data() {
        return {
            offset: 0,
            contentWidth: 0,
            marqueeWidth: 0,
            animationFrame: null,
            fadingOut: false,
        };
    },
    computed: {
        shouldScroll() {
            return this.contentWidth > this.marqueeWidth; //TODO: redetect when resize
        },
        contentStyle() {
            return {
                transform: this.shouldScroll ? `translateX(-${this.offset}px)` : 'translateX(0px)'
            };
        }
    },
    mounted() {
        this.updateWidths();
        window.addEventListener('resize', this.updateWidths);
        console.log(this.shouldScroll)
        this.startMarquee()
    },
    beforeDestroy() {
        cancelAnimationFrame(this.animationFrame);
        window.removeEventListener('resize', this.updateWidths);
    },
    methods: {
        updateWidths() {
            this.contentWidth = this.$refs.marquee.querySelector('.marquee-content').clientWidth;
            this.marqueeWidth = this.$refs.marquee.clientWidth;
        },
        startMarquee() {
            const endPosition = this.contentWidth - this.marqueeWidth;

            const animate = () => {
                if (!this.shouldScroll || this.offset >= endPosition) {
                    if (this.offset < endPosition) {
                        this.offset = endPosition; // 确保内容末尾与容器右边对齐
                    }
                    cancelAnimationFrame(this.animationFrame);
                    setTimeout(() => {
                        this.fadingOut = true;
                        setTimeout(() => {
                            this.offset = 0;
                            this.fadingOut = false;
                            setTimeout(this.startMarquee, this.startDelay + this.fadeDuration); // 淡出后等待，然后重新开始
                        }, this.fadeDuration);
                    }, this.endDelay); // 滚动结束后等待指定时间再淡出
                    return;
                }

                this.offset += this.speed;
                this.animationFrame = requestAnimationFrame(animate);
            };
            console.log(this.startDelay);
            setTimeout(animate, this.startDelay);
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