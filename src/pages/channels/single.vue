<template lang="pug">
main
  section(v-if="channel")
    .hero.is-medium(:style="channelStyle")
      .hero-body
    .info
      .container
        .media
          .media-left
            .image.logo
              span.logo__wrapper
              img(:src="channel.thumbnails.medium.url",:alt="channel.title + ' channel logo'")
          .media-content
            .content
              .title.is-3.channel-title
                strong {{ channel.title }}
                = ' '
                a.ext-link(:href="channelLink",target="_blank",rel="noopener noreferrer")
                  span.icon
                    ion-icon(name="link")
    nav.nav.has-underline
      .container
        .nav-center
          router-link.nav-item.is-tab(:to="{ name: 'channel-about' }") About
          router-link.nav-item.is-tab(:to="{ name: 'channel-recent-videos' }") Uploads
    router-view
</template>

<script>
import store from '~store'
import config from '~config'

export default {
  data() {
    return {
      channel: null,
      params: {
        title: '',
        description: ''
      }
    }
  },
  computed: {
    channelStyle() {
      if(this.channel.image) {
        return { backgroundImage: 'url(' + this.channel.image.bannerTabletImageUrl + ')' }
      }

      return { backgroundColor: this.channel.profileColor }
    },
    channelLink() {
      return `https://www.youtube.com/channel/${this.channel.id}`
    }
  },
  created() {
    this.$Progress.start()
    this.loadChannel()
  },
  watch: {
    $route() {
      this.loadChannel()
    }
  },
  methods: {
    loadChannel() {
      store.fetchChannel(this.$route.params.id).then(channel => {
        this.params.title = channel.title
        this.params.description = `${channel.title} videos`
        this.channel = channel
        this.$emit('updateHead')
      }).catch(() => {
        this.$Progress.fail()
      })
    }
  },
  head: {
    title() {
      return {
        inner: this.params.title,
        separator: '-',
        complement: config.app.name
      }
    },
    meta() {
      return [
        { id: 'description', name: 'description', content: this.params.description }
      ]
    }
  }
}
</script>

<style lang="sass" scoped>
$tablet: 769px

.hero
  background-size: cover

.hero.is-medium .hero-body
  @media (min-width: $tablet)
    padding: 90px

.info
  padding: 20px 20px 0
  .container
    @media (min-width: $tablet)
      height: 60px

.logo
  background-color: #fff
  border-radius: 50%
  width: 64px
  height: 64px
  @media (min-width: $tablet)
    z-index: 2
    border: 5px solid #fff
    top: -150px
    width: 200px
    height: 200px
    // box-shadow: 0 1px 2px rgba(0,0,0,.1)

  &__wrapper
    display: inline-block
    height: 100%
    vertical-align: middle

  img
    display: inline-block
    vertical-align: middle
    border-radius: 50%
    max-height: 100%
    max-width: 100%

.media-left
  margin-right: 20px
  @media (min-width: $tablet)
    margin-right: 30px

.channel-title
  display: inline-flex
  align-items: center

.ext-link
  border-bottom: none !important
  display: inline-flex
  > .icon
    color: #bbbbbb
    font-weight: 600
    font-size: 24px
    margin-left: 10px
</style>
