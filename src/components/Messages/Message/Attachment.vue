<template>
  <div class="attachment">
    <div
      v-if="canPreview"
      class="actions"
    >
      <a :href="attachment.downloadUrl">
        <button class="action">
          <font-awesome-icon
            :icon="['fas', 'file-download']"
            :title="$t('message.file.download', { label: attachment.name })"
          />
        </button>
      </a>
    </div>

    <template v-if="canPreview">
      <!--
        class used for testing;
        Manual stubbing failed to resolve this component
        @todo improve
      -->
      <c-preview-inline
        class="test-inline-preview"
        :src="baseURL + inlineUrl"
        :meta="inlineMeta"
        :name="attachment.name"
        :alt="attachment.name"
        :labels="previewLabels"
        @openPreview="openPreview"
      />
    </template>
    <template v-else-if="attachment.meta.original">
      <span class="no-preview">
        <!-- file has size but not image -->
        <a :href="baseURL + attachment.downloadUrl">
          <div>
            <font-awesome-icon
              :icon="['far', 'file-'+ext]"
              :title="$t('message.openBookmarks')"
            />
          </div>
          <div>
            <span>{{ $t('message.file.download', { label: attachment.name }) }}</span><br>
            <span class="meta">{{ $t('message.file.size', { size: numeral(attachment.meta.original.size).format('0b') }) }}</span>
          </div>
        </a>
      </span>
    </template>
    <template v-else>
      <!-- @todo : added file has no size so probably error, maybe other possible errors -->
      <span class="missing">
        <i18next
          path="message.file.missing"
          tag="i"
        >
          <template><br>{{ attachment.name }}<br></template>
        </i18next>
      </span>
    </template>
  </div>
</template>
<script>
import * as numeral from 'numeral'
import { components } from '@cortezaproject/corteza-vue'
const { CPreviewInline, canPreview } = components

export default {
  components: {
    CPreviewInline,
  },

  props: {
    attachment: {
      type: Object,
      default: undefined,
    },
  },

  computed: {
    baseURL () {
      return window.MessagingAPI
    },

    inlineMeta () {
      return this.attachment.meta
    },
    inlineUrl () {
      return this.ext === 'pdf' ? this.attachment.downloadUrl : this.attachment.previewUrl
    },
    canPreview () {
      const meta = this.attachment.meta
      const type = (meta.preview || meta.original || {}).mimetype
      return canPreview({ type, src: this.inlineUrl, name: this.attachment.name })
    },
    ext () {
      const meta = this.attachment.meta
      switch (meta && meta.original ? meta.original.ext : null) {
        case 'odt':
        case 'doc':
        case 'docx':
          return 'word'
        case 'pdf':
          return 'pdf'
        case 'ppt':
        case 'pptx':
          return 'powerpoint'
        case 'zip':
        case 'rar':
          return 'archive'
        case 'xls':
        case 'xlsx':
        case 'csv':
          return 'excel'
        case 'mov':
        case 'mp3':
        case 'mp4':
          return 'video'
        default: return 'alt'
      }
    },

    previewLabels () {
      return {
        loading: this.$t('preview.pdf.loading'),
        noPages: this.$t('preview.pdf.noPages'),
        firstPagePreview: this.$t('preview.pdf.firstPagePreview'),
        pageLoadFailed: this.$t('preview.pdf.pageLoadFailed'),
        pageLoading: this.$t('preview.pdf.pageLoading'),
        clickToRetry: this.$t('preview.pdf.clickToRetry'),
      }
    },
  },

  methods: {
    openPreview (e) {
      this.$bus.$emit('$message.previewAttachment', Object.freeze({
        ...this.attachment,
        ...e,

        src: this.baseURL + this.attachment.url,
        download: this.baseURL + this.attachment.downloadUrl,
      }))
    },

    numeral: numeral,
  },
}
</script>

<style lang="scss" scoped>
.missing {
  color: #ccc;
}

img {
  max-width: 100% !important;
  width:auto;
  max-height: 180px !important;
}

a{
  text-decoration: none;
  display: block;
  color: #000;
  .meta{
    font-weight: 300;
  }
}

.attachment {
  margin-bottom: 5px;

  &:hover .actions {
    display: block;
  }

  .no-preview a {
    display: flex;
  }
  .no-preview .svg-inline--fa{
    height: 30px;
    width: auto;
    margin-right: 10px;
  }
  .actions {
    position: absolute;
    right: 0;
    top: 0;
    padding: 9px 8px;
    display: none;
    z-index: 2;

    button {
      background-color: #FFFFFF;
      border: solid 1px $secondary;
      border-radius: 4px;
      padding: 3px 7px;
      cursor: pointer;
    }

    .action {
      display: inline-block;
      border: solid 1px rgba($secondary, 0.25);
      border-radius: 5px;
      width: 25px;
      height: 25px;
      background-color: $white;
      font-size: 15px;
      text-align: center;
      box-shadow: 0 0 3px 0 rgba($secondary, 0.5);
      cursor: pointer;
      color: $secondary;
      margin-right: 1px;
      &.unread{
        color: $danger;
      }
      &:hover{
        border-color: $secondary;
      }
    }
  }
}

</style>
