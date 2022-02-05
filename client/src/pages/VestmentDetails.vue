<template>
  <div class="vestment-content" >
    <section class="image-container">
      <div>
        <img class="details-image" :src="vestment.image" :alt="vestment.name" />
        
        </div>
    </section>
    <section class="details">
      <div class="flex-row space"></div>
      <div>
        <h3>{{ vestment.name }}</h3>
        <p> {{ vestment.description }}</p>
        <h4>${{ vestment.price }}</h4>
        <h4>{{ vestment.size }}</h4>
        <h4>Quantity: {{ vestment.quantity }}</h4>
        <button @click="buyVestment(vestment.id)">Purchase</button>
      </div>
    </section>
  </div>
</template>

<script>
import axios from 'axios'
const API_URL = process.env.VUE_APP_BASE_URL
const authUser = process.env.VUE_APP_AUTH_USER
const authPassword = process.env.VUE_APP_AUTH_PASSWORD
  
export default {
  name: 'VestmentDetails',
  data: () => ({
    vestment: null
  }),
  mounted: async function() {
    await this.getVestmentDetails()
  },
  methods: {
    async getVestmentDetails() {
      const vestmentId = parseInt(this.$route.params.vestment_id)
      const res = await axios.get(`${API_URL}/items/${vestmentId}`)
      this.vestment = res.data
    },
    async buyVestment(vestmentId) {
      if (this.vestment.quantity === 1) {
        await axios.delete(`${API_URL}/items/${vestmentId}`, {
        auth: {
          username: authUser,
          password: authPassword
        }
        })
        .then(() => {
          alert(`${this.vestment.name} has been deleted.`)
          this.$router.push('/')
        })
      } else {
        await axios.put(`${API_URL}/items/${vestmentId}`, { ...this.vestment,
          quantity: this.vestment.quantity -= 1
        }, {
          auth: {
            username: authUser,
            password: authPassword
          }
        })
        .then(() => {
          alert(`Your ${this.vestment.name.toLowerCase()} has been purchased!`)
          this.getVestmentDetails()
        })
      }
    }
  }
}

</script>