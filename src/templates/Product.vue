<template>
  <Layout>
    <div class="container-inner mx-auto my-16">
      <h1 class="text-4xl font-bold leading-tight">{{ product.title }}</h1>
      <div class="text-xl text-gray-600 mb-4"></div>
      <div v-if="currentVariant" class="flex mb-8 text-sm">
        <g-link
          class="bg-gray-300 rounded-full px-4 py-2 mr-4 hover:bg-green-300">
          {{ currentVariant.price.amount }}
        </g-link>
      </div>
      
      <div class="mb-8">
        <g-image
              :src="product.images[0].src"
              :alt="product.images[0].altText || product.title" />
      </div>

      <div class="mb-8" v-html="product.descriptionHtml" />

      <div
        v-for="option in productOptions"
        :key="option.id"
        class="flex mx-auto">
        <div class="">
          <label
            :for="option.name"
            class="">
            {{ option.name }}
            <div class="">
              <select
                :id="option.name"
                v-model="selectedOptions[option.name]">
                <option
                  v-for="value in option.values"
                  :key="value"
                  :value="value">
                  {{ value }}
                </option>
              </select>
            </div>
          </label>
        </div>
      </div>
      <br>

      <div class="flex mx-auto">
        <div class="w-1/2">
          <input
            id="quantity"
            v-model.number="quantity"
            class="bg-white focus:outline-none focus:shadow-outline border border-gray-300 rounded-lg py-2 px-4 block w-full appearance-none leading-normal"
            type="number">
        </div>
        <div class="w-1/2">
          <button @click="addToCart"
            @keyup.enter="addToCart" 
            class="bg-black hover:bg-gray-700 text-white font-bold py-2 px-4 border border-blue-700 rounded">
            Add To Cart
          </button>
        </div>
      </div>
      <br>

    </div>
  </Layout>
</template>

<script>
export default {
  metaInfo () {
    return {
      title: this.$page.shopifyProduct.title
    }
  },
  data: () => ({
    selectedOptions: {},
    quantity: 1
  }),
  computed: {
    product () { return this.$page.shopifyProduct },
    productOptions () { return this.product.options.filter(({ name }) => name !== 'Title') },
    currentVariant () {
      const matchedVariant = this.product.variants.find(variant =>
        variant.selectedOptions.every(({ name, value }) => value === this.selectedOptions[ name ])
      )
      return matchedVariant
    }
  },
  watch: {
    $route (to, from) {
      const [firstVariant] = this.product.variants
      this.selectedOptions = firstVariant.selectedOptions.reduce((options, { name, value }) => ({ [ name ]: value, ...options }), {})
    }
  },
  mounted () {
    const [firstVariant] = this.product.variants
    this.selectedOptions = firstVariant.selectedOptions.reduce((options, { name, value }) => ({ [ name ]: value, ...options }), {})
  },
  methods: {
    async addToCart () {
      const variant = this.currentVariant
      const payload = {
        qty: this.quantity,
        productTitle: this.product.title,
        variantTitle: variant.title,
        variantId: variant.id,
        price: variant.price,
        image: variant.image
      }
      await this.$store.commit('addToCart', payload)
      this.$notify({
        title: `Added ${payload.productTitle} to Cart`,
        type: 'primary'
      })
    }
  }
}
</script>

<page-query>
query Product ($id: ID!) {
  shopifyProduct (id: $id) {
    id
    descriptionHtml
    title
    tags
    images(limit: 4) {
      id
      altText
      src: transformedSrc(maxWidth: 600, maxHeight: 400, crop: CENTER)
      thumbnail: transformedSrc(maxWidth: 150, maxHeight: 150, crop: CENTER)
    }
    options {
      id
      name
      values
    }
    variants {
      id
      title
      price {
        amount(format: true)
      }
      selectedOptions {
        name
        value
      }
      image {
        id
        altText
        thumbnail: transformedSrc(maxWidth: 150, maxHeight: 150, crop: CENTER)
      }
    }
  }
}
</page-query>
