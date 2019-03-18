<template>
  <div class="products">
    <h3>Productos</h3>
    <div class="card">
      <div class="card-header">Agregar un nuevo producto</div>
      <div class="card-body">
        <form class="form-inline" v-on:submit.prevent="onSubmit">
          <div class="form-group">
            <label>Nombre</label>
            <input
              v-model="productData.product_name"
              type="text"
              class="form-control ml-sm-2 mr-sm-4 my-2"
              required
            >
          </div>
          <div class="form-group">
            <label>Cantidad</label>
            <input
              v-model="productData.product_quantity"
              type="text"
              class="form-control ml-sm-2 mr-sm-4 my-2"
              required
            >
          </div>
          <div class="form-group">
            <label>Precio</label>
            <input
              v-model="productData.product_price"
              type="text"
              class="form-control ml-sm-2 mr-sm-4 my-2"
              required
            >
          </div>
          <div class="ml-auto text-right">
            <button type="submit" class="btn btn-primary my-2">Agregar</button>
          </div>
        </form>
      </div>
    </div>
    <div class="card mt-5">
      <div class="card-header">Lista de Productos</div>
      <div class="card-body">
        <div class="table-responsive">
          <table class="table">
            <thead>
              <tr>
                <th scope="col">ID</th>
                <th>Nombre</th>
                <th>Cantidad</th>
                <th>Precio</th>
                <th>Acción</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="product in sortedProducts" v-bind:key="product.id">
                <template v-if="editId == product.id">
                  <td>
                    <input v-model="editProductData.product_id" type="text">
                  </td>
                  <td>
                    <input v-model="editProductData.product_name" type="text">
                  </td>
                  <td>
                    <input v-model="editProductData.product_quantity" type="text">
                  </td>
                  <td>
                    <input v-model="editProductData.product_price" type="text">
                  </td>
                  <td>
                    <span class="icon">
                      <i @click="onEditSubmit(product.id)" class="fa fa-check"></i>
                    </span>
                    <span class="icon">
                      <i @click="onCancel" class="fa fa-ban"></i>
                    </span>
                  </td>
                </template>
                <template v-else>
                  <td>{{product.id}}</td>
                  <td>{{product.name}}</td>
                  <td>{{product.quantity}}</td>
                  <td>{{product.price}}</td>
                  <td>
                    <a href="#" class="icon">
                      <i v-on:click="onDelete(product.id)" class="fa fa-trash"></i>
                    </a>
                    <a href="#" class="icon">
                      <i v-on:click="onEdit(product)" class="fa fa-pencil"></i>
                    </a>
                    <router-link
                      :to="{
                      name:'ProductPage', 
                      params:{id: product.id}
                    }"
                      class="icon"
                    >
                      <i class="fa fa-eye"></i>
                    </router-link>
                  </td>
                </template>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Products",
  data() {
    return {
      editId: "",
      productData: {
        id: "",
        product_name: "",
        product_quantity: "",
        product_price: ""
      },
      editProductData: {
        id: "",
        product_name: "",
        product_quantity: "",
        product_price: ""
      },
      products: []
    };
  },
  created() {
    this.getProducts();
  },
  computed: {
    sortedProducts() {
      return this.products.slice().sort((a, b) => {
        return a.product_id - b.product_id;
      });
    }
  },
  methods: {
    async getProducts() {
      const products = [];
      // querySnapshot.forEach((doc)=>{
      //   products.push(doc.data())
      // })
      const querySnapshot = await axios({
        url: "http://localhost:3000/api/productos",
        method: "GET"
        // headers: {}
      });

      const productsArray = [];
      let i = 0;
      for (let i = 0; i < querySnapshot.data.length; i++) {
        productsArray.push(querySnapshot.data[i]);
        products.push(productsArray[i]);
        // Console:
        console.log(querySnapshot.data[i]);
      }

      this.products = products;
    },
    async onSubmit() {
      const querySnapshot = await axios({
        url: "http://localhost:3000/api/productos",
        method: "POST",
        // headers: {},
        data: {
          name: this.productData.product_name,
          quantity: this.productData.product_quantity,
          price: this.productData.product_price
        }
      });
      this.getProducts();
      this.productData.product_name = "";
      this.productData.product_quantity = "";
      this.productData.product_price = "";
    },
    async onDelete(product_id) {
      const querySnapshot = await axios({
        url: `http://localhost:3000/api/productos/${product_id}`,
        method: "DELETE"
        // headers: {},
      });
      this.getProducts();
    },
    onEdit(product) {
      this.editId = product.id;
      this.editProductData.product_id = product.id;
      this.editProductData.product_name = product.name;
      this.editProductData.product_quantity = product.quantity;
      this.editProductData.product_price = product.price;
    },
    onCancel() {
      this.editId = "";
      this.editProductData.product_id = "";
      this.editProductData.product_name = "";
      this.editProductData.product_price = "";
    },
    async onEditSubmit(id) {
      const querySnapshot = await axios({
        url: "http://localhost:3000/api/productos",
        method: "PUT",
        // headers: {},
        data: {
          id: id,
          name: this.editProductData.product_name,
          quantity: this.editProductData.product_quantity,
          price: this.editProductData.product_price
        }
      });
      this.getProducts();
      this.editId = "";
      this.editProductData.product_id = "";
      this.editProductData.product_name = "";
      this.editProductData.product_price = "";
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
.icon {
  margin-right: 10px;
}
.icon i {
  cursor: pointer;
}
</style>
