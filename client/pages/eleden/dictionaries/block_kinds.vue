<template lang="pug">
  universal-dictionary(
    @update-drawer="$emit('update-drawer')"
    :bread-crumbs="bc"
    :query="require('~/gql/eleden/queries/education/block_kinds.graphql')"
    :headers="['id', 'name']"
    query-name="blockKinds"
  )
</template>

<script lang="ts">
import type { PropType, ComputedRef } from '#app'
import { defineComponent, computed, useNuxt2Meta } from '#app'
import { BreadCrumbsItem } from '~/types/devind'
import { useI18n } from '~/composables'
import UniversalDictionary from '~/components/eleden/dictionaries/UniversalDictionary.vue'

export default defineComponent({
  components: { UniversalDictionary },
  middleware: ['auth'],
  props: {
    breadCrumbs: { type: Array as PropType<BreadCrumbsItem[]>, required: true }
  },
  setup (props) {
    const { t, localePath } = useI18n()
    useNuxt2Meta({ title: t('dictionaries.blockKinds.header') as string })

    const bc: ComputedRef<BreadCrumbsItem[]> = computed<BreadCrumbsItem[]>(() => ([
      ...props.breadCrumbs,
      {
        text: t('dictionaries.blockKinds.header') as string,
        to: localePath({ name: 'eleden-dictionaries-block_kinds' }),
        exact: true
      }
    ]))

    return { bc }
  }
})
</script>
