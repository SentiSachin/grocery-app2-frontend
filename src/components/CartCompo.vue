<template>
  <div class="d-flex justify-content-center align-items-center">
    <div class="card p-4">
      <div class="d-flex justify-content-between mb-3 w-75">
          <button type="button" class="btn-close position-absolute top-0 end-0 m-3" aria-label="Close" @click="goBack"></button>
      </div>
      <!-- Cart Summary -->
      <div class="container mt-5">
        <h2 class="mb-4">Your Shopping Cart</h2>
        <ul class="list-group">
          <li v-for="(item, index) in this.$store.state.cartItems" :key="index" class="list-group-item d-flex justify-content-between align-items-center">
            <div class="d-flex align-items-center">
              <span>{{ item.item.name }}</span>
            </div>
            <div class="btn-group" role="group" aria-label="Actions">
              <button type="button" class="btn btn-light">{{ item.Qty }}</button>
              <button type="button" class="btn btn-success" @click="againAddClick(item.id, item.Qty)">Add</button>
              <button type="button" class="btn btn-danger" @click="removeClick(item.id)">Remove</button>
            </div>
          </li>
        </ul>
        <div class="mt-3">
          <a v-if="this.$store.state.cartItems.length > 0" class="btn btn-primary" @click="goToPay">Proceed to Checkout</a>
          <a v-else class="btn btn-warning" disabled>Your Cart is Empty</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: "",
      password: "",
      rememberMe: false,
      authenticated: false,
      role:false,
      products_list: []
    };
  },
  methods: {   
    againAddClick(id, qty){
        for(let i=0;i<this.products_list.length;i++){
        if(this.products_list[i].id==id){
          console.log(this.products_list[i].stock, qty, "sachin")
          if(this.products_list[i].stock > qty)
            this.$store.commit('addAgain',id)
          else{
            alert("Quantity exceeded")
          }
        }
      }
        
    },
    removeClick(id){
        this.$store.commit('removeFromCart',id)
    },    
    goToPay(){
      if(this.$route.path!=='/user/pay')
        this.$router.push('/user/pay')
    },
    goBack(){
      this.$router.go(-1)
    },
    goHome(){
      if(this.$route.path!=='/new/view')
      this.$router.push('/new/view')
    },
      async loadProducts() {
      try {
        const response = await fetch('http://127.0.0.1:8000/api/products/getters?request_type=all', {
          method: 'GET',
          headers: {
            'Authentication-Token': this.$store.getters.authenticateUser.auth_token,
            'Content-Type': 'application/json',
          },
        });
        if (response.status === 200) {
          const data = await response.json();
          console.log(data, "categories fetched")
          this.products_list=data;
        } else {
          const data = await response.json();
          alert(data.message);
        }
      } catch (error) {
        console.error(error);
      }
    },         
  },
  mounted(){
    this.loadProducts()
  }
};
</script>

<style scoped>
/* Add your custom styles here */
.card {
  max-width: 400px;
  width: 100%;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
}
</style>
