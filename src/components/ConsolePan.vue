<template>
  <div
    class="console-pan"
    :class="{'highlight-pan': isHighlightPan}"
    @click="setHighlightPan('console')"
    :style="style">
    <div class="pan-head">
      <el-badge
        :value="logs.length"
        :max="99">
        Console
      </el-badge>
      <el-button
        icon="delete2"
        size="mini"
        v-show="logs.length"
        @click="clearLogs">
        Clear
      </el-button>
    </div>
    <div class="console-logs" ref="console">
      <div
        class="console-log cm-s-default"
        :class="`console-log-${log.type}`"
        key="index"
        v-html="highlight(log.message)"
        v-for="(log, index) in logs">
      </div>
    </div>
    <pan-resizer :enable="enableResizer" />
  </div>
</template>

<script>
  import { mapState, mapActions } from 'vuex'
  import { Badge, Button } from 'element-ui'
  import panPosition from '@/utils/pan-position'
  import PanResizer from '@/components/PanResizer.vue'
  import { hasNextPan } from '@/utils'
  import CodeMirror from 'codemirror'
  import '@/utils/highlight'

  export default {
    watch: {
      logs() {
        const { console } = this.$refs
        this.$nextTick(() => {
          console.scrollTop = console.scrollHeight
        })
      }
    },
    computed: {
      ...mapState(['logs', 'visiblePans', 'highlightPan']),
      style() {
        return panPosition(this.visiblePans, 'console')
      },
      enableResizer() {
        return hasNextPan(this.visiblePans, 'console')
      },
      isHighlightPan() {
        return this.highlightPan === 'console'
      }
    },
    methods: {
      ...mapActions(['clearLogs', 'setHighlightPan']),
      highlight(value) {
        return CodeMirror.highlight(value, { mode: 'javascript' })
      }
    },
    components: {
      'el-badge': Badge,
      'el-button': Button,
      'pan-resizer': PanResizer
    }
  }
</script>

<style scoped>
  .console-logs {
    height: calc(100% - 40px);
    overflow: auto;
  }

  .console-log {
    white-space: pre;
    font-size: 13px;
    padding: 10px;
    border-bottom: 1px solid #efefef;
  }

  .console-log-error {
    color: red;
  }
</style>
