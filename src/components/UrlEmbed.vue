<template>
    <div
        :key="url"
        class="kiwi-mediaviewer-iframely"
    >
        <a
            ref="iframelyLink"
            :href="url"
            :data-iframely-url="iframelyUrl"
            class="kiwi-iframely-card"
        >{{ $t('media_loading', {url: url}) }}</a>
    </div>
</template>

<script>
'kiwi public';

let iframelyTagIncluded = false;

export default {
    props: ['url', 'showPin', 'iframeSandboxOptions'],
    data() {
        return {
            iframelyObject: null,
            waitTimer: 0,
            waitCount: 0,
        };
    },
    computed: {
        iframelyApiKey() {
            return this.$state.setting('buffers.iframelyApiKey');
        },
        iframelyUrl() {
            return `https://cdn.iframe.ly/api/iframely?url=${encodeURIComponent(this.url)}&api_key=${this.iframelyApiKey}`;
        },
    },
    watch: {
        url() {
            this.cleanEmbed();
            this.updateEmbed();
        },
    },
    created() {
        this.updateEmbed();
    },
    beforeDestroy() {
        this.cleanEmbed();
    },
    methods: {
        updateEmbed() {
            let checkIframelyAndShowCard = () => {
                if (typeof window.iframely !== 'function') {
                    if (this.waitTimer) {
                        clearTimeout(this.waitTimer);
                        this.waitTimer = 0;
                    }
                    if (this.waitCount < 300) {
                        this.waitCount++;
                        this.waitTimer = setTimeout(checkIframelyAndShowCard, 100);
                    }
                    return;
                }

                this.$nextTick(() => {
                    this.iframelyObject = window.iframely('card', this.$refs.iframelyLink);
                    if (!this.iframelyObject) {
                        if (this.showPin) {
                            this.$emit('close');
                        }
                        return;
                    }

                    this.iframelyObject.on('card.error', (iframe) => {
                        if (this.showPin) {
                            this.$emit('close');
                        }
                    });

                    this.$emit('setHeight', 'auto');

                    if (this.showPin) {
                        this.$el.style.maxHeight = '400px';
                    } else {
                        this.$emit('setMaxHeight', '54%');
                    }
                });
            };

            if (!iframelyTagIncluded) {
                const head = document.getElementsByTagName('head')[0];
                const script = document.createElement('script');
                script.type = 'text/javascript';
                const iframelyUrl = this.$state.getSetting('settings.iframely.script') ||
                    '//cdn.iframe.ly/embed.js';
                script.src = iframelyUrl;
                head.appendChild(script);
                iframelyTagIncluded = true;
            }
            checkIframelyAndShowCard();
        },
        cleanEmbed() {
            if (this.waitTimer) {
                clearTimeout(this.waitTimer);
                this.waitTimer = 0;
            }
            if (this.iframelyObject) {
                this.iframelyObject.remove();
                this.iframelyObject = null;
            }
        },
    },
};

</script>

<style>

    .kiwi-iframely-card {
        display: block;
        margin: 4px 0;
    }

    .kiwi-mediaviewer-iframely {
        display: inline-block;
        overflow: auto;
    }

    .kiwi-main-mediaviewer .kiwi-mediaviewer-iframely {
        display: block;
    }

</style>
