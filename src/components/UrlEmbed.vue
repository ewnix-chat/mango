<template>
    <div class="url-embed" v-if="response">
        <div v-html="response.html"></div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    props: {
        url: {
            type: String,
            required: true,
        },
    },
    data() {
        return {
            response: null,
        };
    },
    async mounted() {
        try {
            const apiKey = this.$state.setting('iframelyApiKey');
            const result = await axios.get(`https://iframe.ly/api/iframely?url=${this.url}&api_key=${apiKey}`);
            this.response = result.data;
        } catch (error) {
            console.error('Error fetching embed:', error);
        }
    },
};
</script>
