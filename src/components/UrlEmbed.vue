<template>
  <div :key="url" class="kiwi-mediaviewer-iframely">
    <iframe
      ref="iframelyIframe"
      :src="getIframelyUrl(url)"
      frameborder="0"
      allowfullscreen
      style="width: 100%; height: 100%;"
    ></iframe>
  </div>
</template>

<script>
"kiwi public";

export default {
  props: ["url", "showPin", "iframeSandboxOptions"],
  computed: {
    settings() {
      return this.$state.setting("iframely");
    },
    iframelyApiKey() {
      return this.$state.getSetting('buffers.iframelyApiKey');
    }
  },
  methods: {
    getIframelyUrl(url) {
      return `https://iframe.ly/api/oembed?url=${encodeURIComponent(
        url
      )}&api_key=${this.iframelyApiKey}`;
    },
  },
};
</script>

<style>
  .kiwi-mediaviewer-iframely {
    display: inline-block;
    overflow: auto;
    width: 100%;
    height: 100%;
  }

  .kiwi-main-mediaviewer .kiwi-mediaviewer-iframely {
    display: block;
  }
</style>

