<script setup lang="ts">
import { useShop } from '@/stores/shop'
import { useQuery } from '@vue/apollo-composable'
import { collectionByHandle } from '@/api/products/queries/collection'
import { Collection } from '@/types'

interface CollectionResult {
  collection: Collection
}

const route = useRoute()
const handle = computed(() => route.params.handle)

// get collection
const { result, loading, error, refetch, fetchMore } = useQuery<CollectionResult>(collectionByHandle, {
  handle: handle.value,
  first: 32
})
const collection = computed(() => result.value?.collection)

useHead({
  title: computed(() => collection.value?.seo?.title ?? collection.value?.title ?? null),
  meta: [
    {  name: 'description', content: collection.value?.seo?.description ?? collection.value?.description ?? null },
  ],
})

</script>

<template>
  <div>
    <template v-if="loading">
      loading...
    </template>
    <template v-else-if="!loading && !error && result.collection">
      <CollectionHeader :collection="result.collection" />

      <section class="pt-6 pb-24" aria-labelledby="collection-heading">
        <h2 id="products-heading" class="sr-only">Products</h2>
        <div class="grid grid-cols-1">
          <ProductCard
            v-for="product in result.collection.products.edges"
            :key="product.node.is"
            :product="product.node"
          />
        </div>
      </section>

      <!-- TODO: proper product grid -->
    </template>
    <template v-else>
      <PageMissing />
    </template>
  </div>
</template>
