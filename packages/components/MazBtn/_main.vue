<template>
  <component
    :is="componentType"
    :id="uniqueId"
    v-bind="$attrs"
    class="maz-base-component maz-btn maz-inline-flex"
    :class="[
      classes,
      {
        'maz-text-hidden': loading
      }
    ]"
    :type="isLink ? null : type"
    :disabled="isLink ? null : isDisabled"
    @click="handleClick($event)"
    @mouseenter="emitMouseEnter($event)"
    @mouseleave="emitMouseLeave($event)"
    @focus="emitFocus($event)"
    @blur="emitBlur($event)"
  >
    <div
      v-if="hasLeftIcon() || hasMainIcon()"
      class="maz-flex maz-flex-center maz-btn__icon-left"
      :class="{
        'maz-mr-2': !fab && hasSlotDefault()
      }"
    >
      <!-- Icon slot (`icon-left`) -->
      <slot :name="`icon-left` || `icon`">
        <!-- none -->
        <i class="material-icons">{{ leftIconName || iconName }}</i>
      </slot>
    </div>

    <span
      :class="[textClasses, {'maz-flex-1': hasSlotDefault()}]"
      class="maz-flex maz-align-center maz-h-100 maz-overflow-hidden"
    >
      <slot />
    </span>

    <div
      v-if="hasRightIcon()"
      class="maz-flex maz-flex-center maz-btn__icon-right"
      :class="{
        'maz-ml-2': !fab && hasSlotDefault()
      }"
    >
      <!-- Icon slot (`icon-right`) -->
      <slot :name="`icon-right`">
        <!-- none -->
        <i class="material-icons">{{ rightIconName }}</i>
      </slot>
    </div>
    <!-- Add your button text here -->
    <div
      v-if="loading"
      class="maz-btn__spinner maz-flex maz-flex-center"
    >
      <MazSpinner
        :size="25"
        :color="color"
      />
    </div>
  </component>
</template>

<script>
import MazSpinner from '../MazSpinner'
import uniqueId from './../../mixins/uniqueId'

/**
 * > Simple button component
 */

export default {
  name: 'MazBtn',
  components: {
    MazSpinner
  },
  mixins: [uniqueId],
  inheritAttrs: false,
  props: {
    // is the id of the button
    id: { type: String, default: null },
    // is color type (`primary` / `secondary` / `third` / `success` / `danger` / `grey` / `info` / `warning` / `light` / `dark` / `default` / `white` / `black`)
    color: {
      type: String,
      default: 'primary'
    },
    // is the button type (button, submit or something else)
    type: { type: String, default: 'button' },
    // button size (`xl`, `lg` / `md` / `sm` / `mini`)
    size: { type: String, default: 'md' },
    // is a `boolean` to show the loader & disable it
    loading: { type: Boolean, default: false },
    // is a `boolean` to disable the button
    disabled: { type: Boolean, default: false },
    // apply the outline style
    outline: { type: Boolean, default: false },
    // apply the rounded style
    rounded: { type: Boolean, default: false },
    // apply the fab style
    fab: { type: Boolean, default: false },
    // apply the focus style
    active: { type: Boolean, default: false },
    // take 100% of the width
    block: { type: Boolean, default: false },
    // remove shadow/elevation
    noShadow: { type: Boolean, default: false },
    // should be a [material icon](https://material.io/resources/icons/) name (usefull with fab buttons)
    iconName: { type: String, default: null },
    // should be a [material icon](https://material.io/resources/icons/) name
    leftIconName: { type: String, default: null },
    // should be a [material icon](https://material.io/resources/icons/) name
    rightIconName: { type: String, default: null },
    // align text to left (for block button)
    justifyStart: { type: Boolean, default: false },
    // align text to right (for block button)
    justifyEnd: { type: Boolean, default: false }
  },
  computed: {
    componentType () {
      const { href, to } = this.$attrs
      if (href) return 'a'
      else if (to) return 'router-link'
      return 'button'
    },
    isLink () {
      return this.componentType === 'a'
    },
    isDisabled () {
      const { disabled, loading } = this
      return loading || disabled
    },
    classes () {
      const { color, size, outline, rounded, fab, active, block, noShadow, hasRightIcon, hasLeftIcon, hasIcon, hasSlotDefault } = this
      return [
        ...(color ? [`maz-btn--${color}`] : [null]),
        ...(size ? [`maz-btn--${size}`] : [null]),
        ...(outline ? ['maz-btn--outline'] : [null]),
        ...(rounded ? ['maz-btn--rounded'] : [null]),
        ...(block ? ['maz-btn--block'] : [null]),
        ...(fab ? ['maz-btn--fab'] : [null]),
        ...(active ? ['maz-active'] : [null]),
        ...(noShadow ? ['maz-no-shadow'] : [null]),
        ...(hasLeftIcon() ? ['maz-btn--icon--left'] : [null]),
        ...(hasRightIcon()  ? ['maz-btn--icon--right'] : [null]),
        ...(hasIcon()  ? ['maz-btn--icon'] : [null]),
        ...(!hasSlotDefault()  ? ['maz-btn--no-text'] : [null])
      ]
    },
    textClasses () {
      const { justifyStart, justifyEnd } = this
      return [
        ...(justifyStart ? ['maz-justify-start'] : [null]),
        ...(justifyEnd ? ['maz-justify-end'] : [null]),
        ...(!justifyStart && !justifyEnd ? ['maz-justify-center'] : [null]),
      ]
    }
  },
  methods: {
    hasSlotDefault () {
      return this.$slots['default']
    },
    hasIcon () {
      return this.hasLeftIcon() || this.hasRightIcon() || this.hasMainIcon()
    },
    hasMainIcon () {
      return this.iconName || this.$slots['icon']
    },
    hasLeftIcon () {
      return this.leftIconName || this.$slots['icon-left']
    },
    hasRightIcon () {
      return this.rightIconName || this.$slots['icon-right']
    },
    handleClick (e) {
      // return click event
      this.$emit('click', e)
    },
    emitMouseEnter (e) {
      // return mouseenter event
      this.$emit('mouseenter', e)
    },
    emitMouseLeave (e) {
      // return mouseleave event
      this.$emit('mouseleave', e)
    },
    emitFocus (e) {
      // return focus event
      this.$emit('focus', e)
    },
    emitBlur (e) {
      // return blur event
      this.$emit('blur', e)
    }
  }
}
</script>
