<template>
  <div class="container-fluid">
    <notifications />
    <Navbar />
    <SubNavbar />
    <section class="section">
      <slot />
    </section>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar'
import SubNavbar from '@/components/SubNavbar'
export default {
  components: { Navbar, SubNavbar }
}
</script>
