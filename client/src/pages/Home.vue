<template>
  <div>
    <div class = "search">
      <form @submit="getSearchResults" >
        <input
          type="text"
          :value="searchQuery"
          @input="handleChange"
        />
        <button type="submit">Fetch</button>
      </form>
      
      <h2 v-if="searched">Search Results</h2>
      <section class="search-results">

        <VestmentCard v-for="vestment in searchResults" :key="vestment.id" :vestment="vestment" @click.native="selectVestment(vestment.id)"/>

      </section>
    </div>
    <div class="departments" v-if="!searched">
      <h2>Departments</h2>
      <section class="department-card-container">
      
        <DepartmentCard v-for="department in departments" :key="department.id" :department="department" @click.native="selectDepartment(department.id, department.name)"/>

      </section>

    </div>
  </div>
</template>

<script> 
import axios from 'axios'
import DepartmentCard from '../components/DepartmentCard.vue'
import VestmentCard from '../components/VestmentCard.vue'
const API_URL = process.env.VUE_APP_BASE_URL

export default {
  name: 'Home',
  components: {
    DepartmentCard,
    VestmentCard
  },
  data: () => ({
    departments: [],
    searchQuery: '',
    searchResults: [],
    searched: false,
    vestments: []
  }),
mounted: async function() {
  await this.getDepartments(),
  await this.getVestments()
},
methods: {
  async getDepartments() {
    const res = await axios.get(`${API_URL}/departments/`)
    this.departments = res.data
  },
async getVestments() {
    const res = await axios.get(`${API_URL}/items/`)
    this.vestments = res.data
  },
  getSearchResults(e) {
    e.preventDefault()
    const res = this.vestments.filter(vestment => vestment.name.toLowerCase().includes(this.searchQuery.toLowerCase()))
    this.searchResults = res
    this.searched = true
    this.searchQuery = ''
  },
  handleChange(e) {
    this.searchQuery = e.target.value
  },
  selectDepartment(departmentId, departmentName) {
    this.$router.push(`/vestments-by-department/${departmentId}/${departmentName}`)
  },
  selectVestment(vestmentId) {
    this.$router.push(`/details/${vestmentId}`)
  }
}

}
</script>