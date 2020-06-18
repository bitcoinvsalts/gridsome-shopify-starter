<template>
  <Layout>

    <div class="hero container-inner mx-auto flex flex-col sm:flex-row justify-between py-16">
      <div class="text-4xl font-bold w-full text-center">
        <div class="leading-tight font-serif">How to deploy a PWA headless <span class="text-green-700">Shopify</span> site using <span class="text-green-700">Gridsome</span>.</div>
      </div>
    </div>

    <div id="products" class="flex flex-wrap mb-4 max-w-5xl mx-auto">
			<div class="md:w-1/2 lg:w-1/3 w-full px-2" v-on:click="selectProduct({ node: product })" v-for="({ node: product }) in featuredProducts">
        <div class="mx-auto max-w-sm overflow-hiddenmx-auto cursor-pointer duration-500 transform hover:scale-110">
          <div class="w-full rounded-xl h-64 bg-no-repeat bg-contain bg-bottom" :style="{'background-image': 'url(' + product.images[0].src + ')' }"></div>
            <div class="px-6 pb-4 pt-8 mx-4 -mt-4 mb-4 relative rounded-xl bg-white opacity-75 hover:opacity-100">
              <div class="font-bold text-xl mb-2">{{ product.title }}</div>
                <div class="flex text-sm">
                  <div class="w-2/3 font-bold">
                    <span v-if="product.priceRange.minVariantPrice.amount">
                      Price : {{ product.priceRange.minVariantPrice.amount }}
                    </span>
                  </div>
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
    title: 'Home'
  },
  computed: {
    featuredProducts () { return this.$page.allShopifyProduct.edges }
  },
  methods: {
		selectProduct(product) {
      this.$router.push({ path: "/product/" + product.node.handle })
    },
  }
}
</script>

<page-query>
query ShopifyProducts {
  allShopifyProduct (limit: 6) {
    edges {
      node {
        id
        title
        handle
        descriptionHtml
        priceRange {
          minVariantPrice {
            amount(format: true)
          }
        }
        images (limit: 1) {
          id
          altText
          src: transformedSrc (maxWidth: 400, maxHeight: 300, crop: CENTER)
        }
      }
    }
  }
}
</page-query>
