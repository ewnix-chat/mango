<template>
    <div class="kiwi-mediaviewer-embedly">
        <div v-html="embedHtml"></div>
    </div>
</template>

<script>
'kiwi public';

import axios from 'axios';

export default {
    props: ['url', 'showPin', 'iframeSandboxOptions'],
    data() {
        return {
            embedHtml: '',
        };
    },
    computed: {
        iframelyApiKey() {
            return this.$state.setting('iframelyApiKey');
        },

        iframelyApiUrl() {
            return `https://iframe.ly/api/iframely?url=${this.url}&api_key=${this.iframelyApiKey}&iframe=card`;
        },
    },
    watch: {
        url() {
            this.getEmbed();
        },
    },
    created() {
        this.getEmbed();
    },
    methods: {
        async getEmbed() {
            try {
                const response = await axios.get(this.iframelyApiUrl);
                if (response.data && response.data.html) {
                    this.embedHtml = response.data.html;
                }
            } catch (error) {
                console.error('Error fetching embed code:', error);
            }
        },
    },
};
</script>

<style>
.kiwi-mediaviewer-embedly {
    display: inline-block;
    overflow: auto;
}

.kiwi-main-mediaviewer .kiwi-mediaviewer-embedly {
    display: block;
}
</style>
