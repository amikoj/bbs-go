<template>
  <div class="comments">
    <load-more
      ref="commentsLoadMore"
      v-if="commentsPage"
      v-slot="{ results }"
      :init-data="commentsPage"
      :params="{ entityType: entityType, entityId: entityId }"
      url="/api/comment/list"
    >
      <ul>
        <li
          v-for="(comment, index) in results"
          :key="comment.commentId"
          class="comment"
          itemprop="comment"
          itemscope
          itemtype="http://schema.org/Comment"
        >
          <adsbygoogle
            v-if="showAd && (index + 1) % 3 === 0 && index !== 0"
            ad-slot="9686141612"
            ad-format="fluid"
            ad-layout-key="-fb+5w+4e-db+86"
          />
          <div class="comment-avatar">
            <img :src="comment.user.smallAvatar" class="avatar" />
          </div>
          <div class="comment-meta">
            <span
              class="comment-nickname"
              itemprop="creator"
              itemscope
              itemtype="http://schema.org/Person"
            >
              <a :href="'/user/' + comment.user.id" itemprop="name">
                {{ comment.user.nickname }}
              </a>
            </span>
            <span class="comment-time">
              <time
                :datetime="
                  comment.createTime | formatDate('yyyy-MM-ddTHH:mm:ss')
                "
                itemprop="datePublished"
                >{{ comment.createTime | prettyDate }}</time
              >
            </span>
            <span class="comment-reply">
              <a @click="reply(comment)">回复</a>
            </span>
          </div>
          <div class="comment-content content">
            <blockquote v-if="comment.quote" class="comment-quote">
              <div class="comment-quote-user">
                <img
                  :src="comment.quote.user.smallAvatar"
                  class="avatar size-20"
                />
                <a class="quote-nickname">{{ comment.quote.user.nickname }}</a>
                <span class="quote-time">
                  {{ comment.quote.createTime | prettyDate }}
                </span>
              </div>
              <div
                v-lazy-container="{ selector: 'img' }"
                v-html="comment.quote.content"
                itemprop="text"
              />
            </blockquote>
            <p
              v-lazy-container="{ selector: 'img' }"
              v-html="comment.content"
            />
          </div>
        </li>
      </ul>
    </load-more>
  </div>
</template>

<script>
import LoadMore from '~/components/LoadMore'
import utils from '~/common/utils'

export default {
  components: {
    LoadMore
  },
  props: {
    entityType: {
      type: String,
      default: '',
      required: true
    },
    entityId: {
      type: Number,
      default: 0,
      required: true
    },
    commentsPage: {
      type: Object,
      default() {
        return {}
      }
    },
    showAd: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    user() {
      return this.$store.state.user.current
    },
    isLogin() {
      return this.$store.state.user.current != null
    }
  },
  methods: {
    append(data) {
      if (!data) return

      this.$refs.commentsLoadMore.unshiftResults(data)
    },
    reply(quote) {
      if (!this.isLogin) {
        utils.toSignin()
      }
      this.$emit('reply', quote)
    },
    cancelReply() {
      this.quote = null
    }
  }
}
</script>

<style scoped lang="scss"></style>
