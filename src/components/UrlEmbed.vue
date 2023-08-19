<template>
    <div
        :key="url"
        class="kiwi-mediaviewer-embedly"
    >
        <iframe
            :src="iframeUrl"
            frameborder="0"
            scrolling="no"
            width="100%"
            height="auto"
            style="border: none; overflow: hidden;"
        ></iframe>
    </div>
</template>

<script>
'kiwi public';

export default {
    props: ['url', 'showPin', 'iframeSandboxOptions'],
    data() {
        return {
            embedlyObject: null,
            waitTimer: 0,
            waitCount: 0,
        };
    },
    computed: {
        settings() {
            return this.$state.setting('embedly');
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
            const apiKey = this.$state.setting('buffers.iframelyApiKey');
            const apiUrl = `https://iframe.ly/api/iframely?url=${encodeURIComponent(this.url)}&api_key=${apiKey}`;

            fetch(apiUrl)
                .then((response) => response.json())
                .then((data) => {
                    const embedHtml = data.html;
                    if (embedHtml) {
                        this.$nextTick(() => {
                            const embedContainer = document.createElement('div');
                            embedContainer.classList.add('embed-container');
                            embedContainer.innerHTML = embedHtml;
                            this.$el.appendChild(embedContainer);
                            this.$emit('setHeight', 'auto');
                            if (this.showPin) {
                                this.$el.style.maxHeight = (this.settings.maxHeight || 400) + 'px';
                            } else {
                                this.$emit('setMaxHeight', '54%');
                            }
                        });
                    } else if (this.showPin) {
                        this.$emit('close');
                    }
                })
                .catch((error) => {
                    console.error('Failed to fetch embed:', error);
                    if (this.showPin) {
                        this.$emit('close');
                    }
                });
        },

        cleanEmbed() {
            if (this.waitTimer) {
                clearTimeout(this.waitTimer);
                this.waitTimer = 0;
            }
            if (this.embedlyObject) {
                this.embedlyObject.remove();
                this.embedlyObject = null;
            }
        },
    },
};

</script>

<style>

.embedly-card {
    display: block;
    margin: 4px 0;
}

.embedly-card-hug {
    border: 1px solid rgba(0, 0, 0, 0.2);
    border-radius: 5px;
    background: #fff;
}

.kiwi-mediaviewer-embedly {
    display: inline-block;
    overflow: auto;
}

.kiwi-main-mediaviewer .kiwi-mediaviewer-embedly {
    display: block;
}

.embed-container {
    position: relative;
    padding-bottom: 56.25%; /* This ratio will fit 16:9 content */
    height: 0;
    overflow: hidden;
    max-width: 100%;
}

.embed-container iframe,
.embed-container object,
.embed-container embed {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
</style>
