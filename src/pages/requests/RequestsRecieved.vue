<template>
  <section>
    <base-card>
      <header>
        <h2>Requests Receieved</h2>
      </header>
      <ul v-if="hasRequests">
        <request-item v-for="request in receievedRequests"
                      :key="request.id"
                      :email="request.userEmail"
                      :message="request.message"
        ></request-item>
      </ul>
      <h3 v-else>You haven't receieved any requests yet</h3>
    </base-card>
  </section>
</template>

<script>
import RequestItem from "../../components/requests/RequestItem.vue";

export default {
  components: {RequestItem},
  computed: {
    receievedRequests() {
      return this.$store.getters['requests/requests']
    },
    hasRequests() {
      return this.$store.getters['requests/hasRequests']
    }
  }
}
</script>

<style scoped>
header {
  text-align: center;
}

ul {
  list-style: none;
  margin: 2rem auto;
  padding: 0;
  max-width: 30rem;
}

h3 {
  text-align: center;
}
</style>