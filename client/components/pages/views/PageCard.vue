<template lang="pug">
  v-row
    v-col
      v-card.mb-3(v-for="page in pages" :key="page.id")
        v-card-title
          nuxt-link(:to="localePath({ name: 'pages-pageId', params: { pageId: page.id }})"
            style="text-decoration: none") {{ page.title }}
          v-spacer
          .caption {{ dateTimeHM(page.updatedAt) }}
        v-divider
        v-card-actions
          v-chip.mr-2(v-for="tag in page.tags" :key="tag.id" small) {{ tag.name }}
          v-spacer
          .caption(v-if="page.signature") {{ page.signature }}
          v-chip.mx-2(v-else-if="page.user" pill)
            v-avatar(v-if="page.user.avatar" left)
              v-img(:src="`/${page.user.avatar}`")
            | {{ $getUserName(page.user) }}
</template>

<script lang="ts">
import type { PropType } from '#app'
import { defineComponent } from '#app'
import { PageType, UserType } from '~/types/graphql'
import EditorTypography from '~/components/common/editor/EditorTypography.vue'
import AddPage from '~/components/pages/AddPage.vue'
import { useFilters } from '~/composables'

export default defineComponent({
  components: { EditorTypography, AddPage },
  props: {
    pages: { type: Array as PropType<(PageType & { user: UserType })[]>, required: true }
  },
  setup () {
    const { dateTimeHM } = useFilters()
    return { dateTimeHM }
  }
})
</script>
