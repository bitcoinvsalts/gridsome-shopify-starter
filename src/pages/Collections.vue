<template>
  <Layout>
    <div class="container-inner mx-auto my-16">
      <div id="collections" class="flex flex-wrap mb-4 max-w-5xl mx-auto">
        <div class="md:w-1/2 lg:w-1/3 w-full px-2" v-on:click="selectCollection({ node: collection })" v-for="({ node: collection }) in collections">
          <div class="mx-auto max-w-sm overflow-hiddenmx-auto cursor-pointer duration-500 transform hover:scale-110">
            <div class="w-full rounded-xl h-64 bg-no-repeat bg-contain bg-bottom" :style="{'background-image': 'url(' + collection.image.src + ')' }"></div>
              <div class="px-6 pb-4 pt-8 mx-4 -mt-4 mb-4 relative rounded-xl bg-white opacity-75 hover:opacity-100">
                <div class="font-bold text-xl mb-2">{{ collection.title }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </Layout>
</template>

<script>
export default {
  metaInfo: {
    title: 'Collections'
  },
  data: () => ({ 
  }),
  computed: {
    collections () { return this.$page.allShopifyCollection.edges },
  },
  methods: {
		selectCollection(product) {
      this.$router.push({ path: "/collection/" + product.node.handle })
    },
  }
}
</script>

<page-query>
query ShopifyProducts {
  allShopifyCollection (limit: 100) {
    edges {
      node {
        id
        handle
        title
        descriptionHtml
        image {
          altText
          src: transformedSrc(maxWidth: 800, maxHeight: 800, crop: CENTER)
        }
      }
    }
  }
}
</page-query>