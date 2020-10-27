
<template>
  <div class="actions">
    <div>
      <i
        class="action icon-smile"
        :title="$t('message.reaction')"
        @click.stop="onReaction"
      />
      <i
        v-if="message.canReply && !hideActionOpenThread"
        class="action icon-message-circle-left-speak"
        :title="$t('message.replyInThread')"
        @click="$emit('openThreadPanel', { message })"
      />
      <i
        v-if="!hideActionGoToMessage"
        class="action icon-circle-right"
        :title="$t('message.goto')"
        @click="$emit('goToMessage', { message })"
      />
      <i
        v-if="!hideMarkAsUnread"
        class="action icon-checkmark"
        :title="$t('message.markAsUnread')"
        @click="$emit('markAsUnread', { message })"
      />
      <font-awesome-icon
        v-if="!hidePinning && !readOnly"
        class="action"
        icon="thumbtack"
        :class="{pinned:message.isPinned}"
        :title="$t('message.pin')"
        @click="$emit('pinMessage', { message })"
      />
      <font-awesome-icon
        v-if="!hideBookmarking"
        class="action"
        :icon="['far', 'bookmark']"
        :class="{bookmarked:message.isBookmarked}"
        :title="$t('message.bookmark')"
        @click="$emit('bookmarkMessage', { message })"
      />
      <i
        v-if="message.canEdit"
        class="action icon-edit"
        :title="$t('message.edit')"
        @click="$emit('editMessage', { message })"
      />
      <i
        v-if="message.canDelete"
        class="action icon-trash"
        :title="$t('message.delete')"
        @click="$emit('deleteMessage', { message })"
      />
    </div>
  </div>
</template>
<script>
export default {
  props: {
    message: {
      type: Object,
      required: true,
    },

    readOnly: Boolean,

    hideActions: Boolean,
    hideMarkAsUnread: Boolean,
    hideActionOpenThread: Boolean,
    hideActionsMenu: Boolean,
    hidePinning: Boolean,
    hideBookmarking: Boolean,
    hideActionGoToMessage: { type: Boolean, default: false },
  },

  methods: {
    onReaction () {
      this.$bus.$emit('ui.openEmojiPicker', {
        callback: ({ colons }) => {
          // Got called back from the emoji picker, now send the reaction to this message...
          this.$emit('reaction', { reaction: colons })
        },
      })
    },
  },
}

</script>
<style scoped lang="scss">
@import 'corteza-webapp-messaging/src/themes/corteza-base/menu-layer.scss';

.actions{
  position: absolute;
  width: auto;
  right: 10px;
  text-align: right;
  top: 0px;
  display: inline-block;
  padding: 0 0.5em;
  z-index: 5;

  .action {
    display: inline-block;
    border: solid 1px rgba($secondary, 0.25);
    border-radius: 5px;
    line-height: 20px;
    width: 25px;
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

.actions {
  min-width: 180px;
  .bookmarked{
    color: $success;
  }
  .pinned {
    color: $warning;
  }
  .svg-inline--fa{
    padding: 4px;
    width: 25px;
    height: 22px;
    margin-bottom: -3px;
  }
}
</style>
