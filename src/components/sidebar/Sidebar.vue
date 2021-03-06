<template lang="pug">
  aside(
    class="sidebar"
    v-bind:class="classes"
    v-bind:id="id"
    v-bind:style="styles"
  )
    slot(name="top")
    v-sidebar-items(
      v-if="items.length > 0"
      v-bind:group-class="groupClass"
      v-bind:items="items"
      v-bind:ripple="ripple"
      v-bind:router="router"
    )
    slot
</template>

<script>
  import Toggleable from '../../mixins/toggleable'

  export default {
    name: 'sidebar',

    mixins: [Toggleable],

    props: {
      closeOnClick: {
        type: Boolean,
        default: true
      },

      drawer: Boolean,

      fixed: Boolean,
      
      groupClass: {
        type: String,
        default: ''
      },

      height: {
        type: String,
        default: '100vh'
      },

      id: {
        type: String,
        required: true
      },

      mobile: {
        type: Boolean,
        default: true
      },

      mobileBreakPoint: {
        type: Number,
        default: 992
      },

      items: {
        type: Array,
        default: () => []
      },

      right: Boolean,

      ripple: Boolean,

      router: Boolean
    },

    computed: {
      classes () {
        return {
          'sidebar--close': !this.active,
          'sidebar--drawer': this.drawer,
          'sidebar--fixed': this.fixed || this.drawer,
          'sidebar--fixed-right': this.fixed && this.right,
          'sidebar--mobile': this.mobile,
          'sidebar--open': this.active
        }
      },

      styles () {
        return {
          'height': this.height
        }
      }
    },

    mounted () {
      this.$vuetify.load(() => {
        this.resize()
        window.addEventListener('resize', this.resize, false)
      })

      this.$vuetify.bus.sub(`${this.$options.name}:item-clicked:${this._uid}`, this.itemClicked)
    },

    beforeDestroy () {
      window.removeEventListener('resize', this.resize)
      this.$vuetify.bus.unsub(`${this.$options.name}:item-clicked:${this._uid}`, this.itemClicked)
    },

    methods: {
      resize () {
        if (this.mobile && !this.drawer) {
          this.active = window.innerWidth >= this.mobileBreakPoint
        }
      },

      itemClicked () {
        if (
          (window.innerWidth < this.mobileBreakPoint && this.mobile && this.closeOnClick)
          || (this.drawer && this.closeOnClick)
        ) {
          this.active = false
        }
      },

      closeConditional (e) {
        return (
          (window.innerWidth >= this.mobileBreakPoint && !this.drawer)
          || !this.closeOnClick
          || this.$el.contains(e.target)
        )
      }
    }
  }
</script>