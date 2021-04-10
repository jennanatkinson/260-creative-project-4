<template>
  <div class="shop">
    <div class="pageHeader">
      <h1 class="center">SHOP</h1>
    </div>
    <div class="content">
      <div id="filterSelector" class="center">
        <p>Display:</p>
        <div id="filterOptions" class="center">
          <div class="center">
            <input type="radio" id="all" value="all" v-model="displayAllRadio" checked="checked" @click="displayAll">
            <label for="all">All</label>
          </div>
          <div class="center">
            <input type="radio" id="favorites" value="favorites" v-model="displayFavoritesRadio" @click="displayFavorites">
            <label for="favorites">Favorites</label>
          </div>
          <div class="center">
            <input type="radio" id="dietarySafe" value="dietarySafe" v-model="displaySafeFoodsRadio" @click="displaySafeFoods">
            <label for="dietarySafe">Dietary Safe</label>
          </div>
        </div>
      </div>
      <ProductList :products="products" />
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import ProductList from "../components/ProductList.vue"
export default {
  name: 'Shop',
  components: {
    ProductList
  },
  data() {
    return {
      displayAllRadio: true,
      displayFavoritesRadio: false,
      displaySafeFoodsRadio: false,
      //products: []
    }
  },
  computed: {
    products() {
      if (this.displayFavoritesRadio) {
        return this.displayFavorites();
      }
      if (this.displaySafeFoodsRadio) {
        return this.displaySafeFoods();
      }
      return this.displayAll();
    },
  },
  methods: {
    async displayAll() {
      this.displayFavoritesRadio = false;
      this.displaySafeFoodsRadio = false;
      try {
        let response = await axios.get('/api/products');
        return this.setFavorites(response.data.products);
      } catch (error) {
        console.log(error);
        return [];
      }
    },
    async displayFavorites() {
      if (this.$root.$data.user === null) {
        alert("Sorry! To use this feature, make an account");
        return (this.displayAll());
      }
      this.displayAllRadio = false;
      this.displaySafeFoodsRadio = false;
      try {
        let response = await axios.get(`/api/users/${this.$root.$data.user._id}/favorite`);
        return this.setFavorites(response.data.products);
      } catch (error) {
        console.log(error);
        return [];
      }
    },
    async displaySafeFoods() {
      if (this.$root.$data.user === null) {
        alert("Sorry! To use this feature, make an account and tell us your dietary restrictions");
        return (this.displayAll());
      }
      this.displayAllRadio = false;
      this.displayFavoritesRadio = false;

      try {
        let response = await axios.get(`/api/users/${this.$root.$data.user._id}/allerginFriendly`);
        return this.setFavorites(response.data.products);
      } catch (error) {
        console.log(error);
        return [];
      }
    },
    setFavorites(products) {
      this.$root.$data.user.favoriteProducts.forEach(element =>{
        let index = products.findIndex(product => product.id === element.id);
        products[index].favorite = true;
      });
      return products;
    }
  }
}
</script>

<style scoped>
#filterSelector {
  display: block;
  align-items: center;
  margin-top: 50px;
  margin-bottom: 100px;
}
#filterOptions {
  display: block;
}
#filterOptions input {
  align-items: center;
}
#filterOptions label {
  padding-left: 5px;
  font-family: 'Prata', serif;
}
.center {
  width: 60%;
}
@media screen and (min-width: 800px) {
  #filterSelector {
    display: flex;
    margin-bottom: 0px;
  }
  #filterOptions {
    display: flex;
  }
}
</style>