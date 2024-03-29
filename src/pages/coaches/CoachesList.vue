<template>
  <base-dialog :show="!!error" title="An error occurred" @close="handleError">
    <p> {{ error }} </p>
  </base-dialog>
  <section>
    <coach-filter @change-filter="setFilters"></coach-filter>
  </section>
  <section>
    <base-card>
      <div class="controls">
        <base-button mode="outline" @click="loadCoaches">Refresh</base-button>
        <base-button v-if="!isCoach && !isLoading" link to="/register">Register as coach</base-button>
      </div>
      <div v-if="isLoading">
        <base-spinner></base-spinner>
      </div>
      <ul v-else-if="hasCoaches">
        <coach-item
            v-for="coach in filteredCoaches"
            :key="coach.id"
            :id="coach.id"
            :first-name="coach.firstName"
            :last-name="coach.lastName"
            :rate="coach.hourlyRate"
            :areas="coach.areas"
        ></coach-item>
      </ul>
      <h3 v-else>No Coaches</h3>
    </base-card>
  </section>
</template>

<script>
import CoachItem from "../../components/coaches/CoachItem.vue";
import BaseButton from "../../components/ui/BaseButton.vue";
import CoachFilter from "../../components/coaches/CoachFilter.vue";
import BaseSpinner from "../../components/ui/BaseSpinner.vue";
import BaseDialog from "../../components/ui/BaseDialog.vue";
import {handleError} from "vue";

export default {
  data() {
    return {
      error: null,
      isLoading: false,
      activeFilters: {
        frontend: true,
        backend: true,
        career: true
      }
    }
  },
  created() {
    this.loadCoaches();
  },
  methods: {
    handleError() {
      this.error = null;
    },
    setFilters(updatedFilters){
      this.activeFilters = updatedFilters
    },
    async loadCoaches() {
      this.isLoading = true;
      try {
        await this.$store.dispatch('coaches/loadCoaches');
      } catch(error) {
        this.error = error.message || 'Something went wrong'
      }
      this.isLoading = false;
    }
  },
  components: {BaseDialog, BaseSpinner, CoachFilter, BaseButton, CoachItem},
  computed: {
    isCoach() {
      return this.$store.getters['coaches/isCoach'];
    },
    filteredCoaches() {
      const coaches = this.$store.getters['coaches/coaches'];
      return coaches.filter(coach => {
        if (this.activeFilters.frontend && coach.areas.includes('frontend')){
          return true;
        }
        if (this.activeFilters.backend && coach.areas.includes('backend')){
          return true;
        }
        if (this.activeFilters.career && coach.areas.includes('career')){
          return true;
        }
        return false;
      })
    },
    hasCoaches() {
      return !this.isLoading && this.$store.getters['coaches/hasCoaches']
    }
  },
}
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

.controls {
  display: flex;
  justify-content: space-between;
}
</style>