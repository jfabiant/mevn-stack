<template>
  <div class="single-product">
    <div v-if="loaded">
      <h3>Detalles del producto</h3>
      <div class="card">
        <div class="card-header">Datos</div>
        <div class="card-body">
          <p>ID: {{productId}}</p>
          <p>Nombre del producto: {{productName}}</p>
          <p>Cantidad del producto: {{productQuantity}}</p>
          <p>Precio del producto: S/. {{productPrice}}</p>
          <button class="btn btn-success">Comprar ahora</button>
          <router-link :to="'/'" class="btn btn-primary">Regresar</router-link>
        </div>
      </div>
    </div>
    <div v-else>
      <h3>Cargando ...</h3>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "ProductPage",
  data() {
    return {
      productId: "",
      productName: "",
      productPrice: "",
      productQuantity: "",
      loaded: false
    };
  },
  async beforeCreate() {
    const querySnapshot = await axios({
      url: `http://localhost:3000/api/productos/${this.$route.params.id}`,
      method: "GET",
      // headers: {},
      data: this.datos
    });

    if (querySnapshot.data !== "") {
      console.log("Datos del producto");
      console.log(querySnapshot.data);

      this.productId = querySnapshot.data.id;
      this.productName = querySnapshot.data.name;
      this.productQuantity = querySnapshot.data.quantity;
      this.productPrice = querySnapshot.data.price;
      this.loaded = true;
    } else {
      console.log("No such document!");
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  text-align: center;
  margin-top: 30px;
  margin-bottom: 20px;
}
</style>
