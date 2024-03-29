<template lang="pug">
  v-dialog(v-if="isVisible(editor)" v-model="isDialog" width="600")
    template(#activator="{ on }")
      v-tooltip(top)
        template(#activator="{ on: onTooltip, attrs }")
          v-btn(v-on="{ ...on, ...onTooltip }" v-bind="attrs" icon @click="onOpen")
            v-icon {{icon}}
        span {{$t('common.richTextEditor.image.changeImage')}}
    validation-observer(v-slot="{ invalid }")
      form(@submit.prevent="onDone")
        v-card
          v-card-title {{$t('common.richTextEditor.image.changeImage')}}
            v-spacer
            v-btn(@click="isDialog=false" icon)
              v-icon mdi-close
          v-card-text
            v-select(v-model="sizeMode" :items="sizeModes")
            validation-provider(:name="$t('common.richTextEditor.image.width')" rules="required|min_value:1" v-slot="{ errors, valid }")
              v-text-field(v-model="width" :label="$t('common.richTextEditor.image.width')" type="number" :error-messages="errors" :success="valid")
            v-btn(:class="{ 'v-btn--active': keepAspectRatio }" @click="onAspectRatioChanged" icon)
              v-icon mdi-link
            validation-provider(:name="$t('common.richTextEditor.image.height')" rules="required|min_value:1" v-slot="{ errors, valid }")
              v-text-field(v-model="height" :label="$t('common.richTextEditor.image.height')" type="number" :disabled="keepAspectRatio" :error-messages="errors" :success="valid")
          v-card-actions
            v-spacer
            v-btn(type="submit" :disabled="invalid" color="primary") {{ $t('common.richTextEditor.image.changeImage') }}
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'
import { Editor } from '@tiptap/core'
import { OnButtonStateChangedType, OnClickType } from '~/components/common/editor/extensions/ExtensionOptionsInterface'

export default Vue.extend<any, any, any, any>({
  props: {
    tooltip: { type: String, required: true },
    icon: { type: String, required: true },
    editor: { type: Object as PropType<Editor>, required: true },
    onClick: { type: Function as PropType<OnClickType>, default: () => null },
    isDisabled: { type: Function as PropType<OnButtonStateChangedType>, default: () => null },
    isActive: { type: Function as PropType<OnButtonStateChangedType>, default: () => null },
    isVisible: { type: Function as PropType<OnButtonStateChangedType>, default: () => null }
  },
  data: () => ({
    isDialog: false,
    image: null,
    keepAspectRatio: false,
    height: 0,
    width: 0,
    sizeMode: 'px'
  }),
  computed: {
    sizeModes () {
      return [
        { text: this.$t('common.richTextEditor.image.px'), value: 'px' },
        { text: this.$t('common.richTextEditor.image.pc'), value: '%' }
      ]
    }
  },
  methods: {
    onOpen () {
      const from = this.editor.state.selection.from
      const to = this.editor.state.selection.to
      this.editor.state.doc.nodesBetween(from, to, (node: any) => {
        if (node.type.name === 'image') {
          this.height = node.attrs.height.toFixed()
          this.width = node.attrs.width.toFixed()
          this.sizeMode = node.attrs.sizeMode
          this.keepAspectRatio = node.attrs.keepAspectRatio
        }
      })
    },
    onAspectRatioChanged () {
      this.keepAspectRatio = !this.keepAspectRatio
    },
    onDone (): void {
      this.editor.chain().focus().updateAttributes('image', { width: this.width, height: this.height, keepAspectRatio: this.keepAspectRatio, sizeMode: this.sizeMode }).run()
      this.isDialog = false
    }
  }
})
</script>
