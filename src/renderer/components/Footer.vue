<template>
  <div class="footer-container">
    <div class="footer-left">
      <div class="footer-status">{{ $t('_status') }}: <span :title="statusMessage">{{ statusMessage }}</span></div>
    </div>
    <div class="footer-right">
      <div class="footer-btn"
        @click="openDevTools()">{{ $t('_dev_tools') }}</div>
      <div class="footer-btn"><a :href="bugsUrl" target="_blank">{{ $t('_report') }}</a></div>
    </div>
  </div>
</template>

<script>
import packageInfo from '@/../../package.json';
import { ipcRenderer } from 'electron';

export default {
  data() {
    return {
      bugsUrl: packageInfo.bugs.url,
      statusMessage: ''
    }
  },

  beforeMount() {
    ipcRenderer.on('debug-service:status', (event, data) => {
      console.log(`[PO] - ${this.statusMessage}`);
      this.statusMessage = data.statusMessage;
    });

    ipcRenderer.on('debug-service:devToolsOpened', (event, data) => {
      this.$emit('devToolsToggled', true);
    });

    ipcRenderer.on('debug-service:devToolsClosed', (event, data) => {
      this.$emit('devToolsToggled', false);
    });
  },

  methods: {
    openDevTools() {
      ipcRenderer.send('debug-service', {
        action: 'openDevTools',
        args: {
          window: 'app'
        }
      });
    }
  }
}
</script>

<style lang="scss">
.footer-container {
  $height: 25px;

  display: flex;
  height: $height;
  overflow: hidden;

  .footer-left {
    //
  }

  .footer-right {
    flex: 1;
    text-align: right;
  }

  .footer-status {
    display: inline-block;
    height: $height;
    line-height: $height;
    vertical-align: top;
    color: #333;
    font-size: 12px;
    padding: 0 8px;
  }

  .footer-btn {
    display: inline-block;
    height: $height;
    font-size: 12px;
    line-height: $height;
    padding: 0 8px;
    cursor: pointer;
    vertical-align: top;
    color: #333;

    &:hover {
      background: #c6c6c6;
    }

    a {
      color: #333;
      text-decoration: none;
    }
  }
}
</style>
