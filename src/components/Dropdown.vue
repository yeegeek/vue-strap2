<template>
  <div :is="isLi?'li':'div'" v-click-outside="blur"
    :class="[{open:show,disabled:disabled,dropdown:isLi,'input-group-btn':inInput,'btn-group':!isLi&&!inInput}]"
  >
    <slot name="before"></slot>
    <slot name="button">
      <a v-if="isLi" role="button" :class="['dropdown-toggle',buttonSize,{disabled:disabled}]" @keyup.esc="show = false">
        {{ text }}
        <span class="caret"></span>
      </a>
      <button v-else type="button" :class="['btn btn-' + type,buttonSize,'dropdown-toggle']" @keyup.esc="show = false" :disabled="disabled">
        {{ text }}
        <span class="caret"></span>
      </button>
    </slot>
    <slot name="dropdown-menu">
      <ul class="dropdown-menu"><slot></slot></ul>
    </slot>
  </div>
</template>
<script>
import $ from './utils/NodeList.js'
import ClickOutside from './../directives/ClickOutside.js'

export default {
  directives: {
    ClickOutside
  },
  props: {
    disabled: {type: Boolean, default: false},
    size: {type: String, default: null},
    text: {type: String, default: null},
    type: {type: String, default: 'default'},
    value: {type: Boolean, default: false}
  },
  data () {
    var show = this.value
    return {show}
  },
  watch: {
    show (val) { this.$emit('input', val) },
    value (val) { this.show = val }
  },
  computed: {
    buttonSize () { return ~['lg', 'sm', 'xs'].indexOf(this.size) ? 'btn-' + this.size : '' },
    inInput () { return this.$parent._input },
    isLi () { return this.$parent._isTabs || this.$parent._navbar || this.$parent.menu },
    menu () { return !this.$parent || this.$parent.navbar },
    slots () { return this._slotContents },
    submenu () { return this.$parent && (this.$parent.menu || this.$parent.submenu) }
  },
  methods: {
    blur () { this.show = false }
  },
  mounted () {
    var $el = $(this.$el)
    $el.findChildren('a,button.dropdown-toggle').on('click', e => {
      e.preventDefault()
      if (!this.disabled) { this.show = !this.show }
      return false
    })
    $el.findChildren('ul').on('click', 'li>a', e => { this.show = false })
  },
  beforeDestroy () {
    $(this.$el).findChildren('a,button,ul').off()
  }
}
</script>
