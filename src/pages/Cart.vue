<template>
  <Layout>
    <div class="container-inner mx-auto my-16">

      <table class="w-full table-auto">
        <thead>
          <tr>
            <th />
            <th class="px-4 py-2">Product</th>
            <th class="px-4 py-2">Quantity</th>
            <th class="px-4 py-2">Total</th>
            <th />
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="item in cart"
            :key="item.variantId">
            <td>
              <g-image
                :src="item.image.thumbnail"
                :alt="item.image.altText || item.title" />
            </td>
            <td>
              {{ item.productTitle }}
              {{ item.variantTitle !== 'Default Title' ? `- ${item.variantTitle}` : '' }}
            </td>
            <td>{{ item.qty }}</td>
            <td>{{ totalPrice(item) }}</td>
            <td>
              <button
                class=""
                @click="removeItem(item.variantId)"
                @keyup="removeItem(item.variantId)">
                <small>Remove</small>
              </button>
            </td>
          </tr>
        </tbody>
        <tfoot v-if="cart.length">
          <tr>
            <th />
            <th />
            <th />
            <th />
            <th class="has-text-right">
              <p>Cart Total: {{ cartTotal }}</p>
            </th>
          </tr>
        </tfoot>
      </table>
      <br>

      <form v-if="cart.length" @submit.prevent="checkout">
        <div class="flex mx-auto">
          <div class="w-1/2">
            <input
              id="email"
              v-model="email"
              class="bg-white focus:outline-none focus:shadow-outline border border-gray-300 rounded-lg py-2 px-4 block w-full appearance-none leading-normal"
              type="email"
              placeholder="Your email address"
              required>
          </div>
          <div class="w-1/2">
            <button type="submit"
              :class="{'is-loading': isLoading}"
              class="bg-black hover:bg-gray-700 text-white font-bold py-2 px-4 border border-blue-700 rounded">
              Checkout
            </button>
          </div>
        </div>
      </form>

      <div
        v-else
        class="txt-xl">
        <p>To checkout, add some items to cart.</p>
        <br>
        <g-link
          to="/"
          class="button is-primary is-outlined">
          Browse
        </g-link>
      </div>

    </div>
  </Layout>
</template>

<script>
import currency from 'currency.js'
import gql from 'graphql-tag'
export default {
  metaInfo: {
    title: 'Your Cart'
  },
  data: () => ({ email: '', isLoading: false }),
  computed: {
    cart () { return this.$store.state.cart },
    cartTotal () {
      const total = this.cart.reduce((total, item) => total.add(currency(item.price.amount).multiply(item.qty)), currency(0, { formatWithSymbol: true, symbol: '$' }))
      return total.format()
    }
  },
  methods: {
    totalPrice ({ qty, price }) {
      return currency(price.amount, { formatWithSymbol: true, symbol: '$' }).multiply(qty).format()
    },
    async removeItem (itemId) {
      await this.$store.commit('removeFromCart', itemId)
      this.$notify({
        title: 'Item removed from cart',
        type: 'primary'
      })
    },
    async checkout () {
      const email = this.email
      if (!this.cart.length) return alert('No items in cart')
      const lineItems = this.cart.map(item => ({ quantity: item.qty, variantId: item.variantId }))

      const checkoutInput = { email, lineItems }

      try {
        this.isLoading = true
        const { data: { checkoutCreate } } = await this.$apollo.mutate({
          mutation: gql`mutation checkoutCreate($input: CheckoutCreateInput!) {
            checkoutCreate(input: $input) {
              checkout {
                id
                webUrl
              }
              checkoutUserErrors {
                code
                field
                message
              }
            }
          }
          `,
          variables: { input: checkoutInput }
        })
        if (checkoutCreate.checkoutUserErrors.length) throw new Error(checkoutCreate.checkoutUserErrors[ 0 ].message)

        window.location = checkoutCreate.checkout.webUrl
      } catch (error) {
        this.isLoading = false
        console.error(error)
        this.$notify({
          title: error,
          type: 'danger',
          message: 'Something went wrong - please try again.'
        })
      }
    }
  }
}
</script>