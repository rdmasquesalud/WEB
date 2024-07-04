<template>
  <div id="app">
    <h1>Productos Store</h1>
    <button @click="fetchData">Cargar Productos</button>
    <button @click="updateData">Guardar Cambios</button>
    <table>
      <thead>
        <tr>
          <th>
            Producto
            <input v-model="filters.name" placeholder="Filtrar por Producto" />
          </th>
          <th>
            Precio
            <input v-model="filters.price" placeholder="Filtrar por Precio" />
          </th>
          <th>
            Descripción
            <input v-model="filters.descripcion" placeholder="Filtrar por Descripción" />
          </th>
          <th>
            CATEGORIA
            <input v-model="filters.CATEGORIA" placeholder="Filtrar por CATEGORIA" />
          </th>
          <th>
            Imagen 1
            <input v-model="filters.Imagen1" placeholder="Filtrar por Imagen 1" />
          </th>
          <th>
            Imagen 2
            <input v-model="filters.Imagen2" placeholder="Filtrar por Imagen 2" />
          </th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(product, index) in filteredProducts" :key="index">
          <td><input v-model="product.name" /></td>
          <td><input v-model="product.price" type="number" /></td>
          <td><input v-model="product.descripcion" /></td>
          <td><input v-model="product.CATEGORIA" /></td>
          <td>
            <input v-model="product.Imagen1" />
            <img :src="product.Imagen1" alt="Imagen 1" width="100" height="100"/>
          </td>
          <td>
            <input v-model="product.Imagen2" />
            <img :src="product.Imagen2" alt="Imagen 2" width="100" height="100"/>
          </td>
          <td>
            <button @click="deleteProduct(index)">Eliminar</button>
          </td>
        </tr>
        <tr v-if="newProduct.isAdding">
          <td><input v-model="newProduct.name" /></td>
          <td><input v-model="newProduct.price" type="number" /></td>
          <td><input v-model="newProduct.descripcion" /></td>
          <td><input v-model="newProduct.CATEGORIA" /></td>
          <td>
            <input v-model="newProduct.Imagen1" />
            <img :src="newProduct.Imagen1" alt="Imagen 1" width="100" height="100"/>
          </td>
          <td>
            <input v-model="newProduct.Imagen2" />
            <img :src="newProduct.Imagen2" alt="Imagen 2" width="100" height="100"/>
          </td>
          <td>
            <button @click="addProduct">Agregar</button>
            <button @click="cancelAdd">Cancelar</button>
          </td>
        </tr>
      </tbody>
    </table>
    <button @click="showAddProduct">Agregar Nueva Línea</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      products: [],
      newProduct: {
        name: '',
        price: '',
        descripcion: '',
        CATEGORIA: '',
        Imagen1: '',
        Imagen2: '',
        isAdding: false
      },
      filters: {
        name: '',
        price: '',
        descripcion: '',
        CATEGORIA: '',
        Imagen1: '',
        Imagen2: ''
      },
      pat: "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",
      sha: ""
    };
  },
  computed: {
    filteredProducts() {
      return this.products.filter(product => {
        return Object.keys(this.filters).every(key => {
          return String(product[key]).toLowerCase().includes(String(this.filters[key]).toLowerCase());
        });
      });
    }
  },
  methods: {
    async fetchData() {
      try {
        const response = await axios.get(
          "https://api.github.com/repos/rdmasquesalud/Productos_Store/contents/productos.json",
          {
            headers: {
              Authorization: `Bearer ${this.pat}`,
              Accept: "application/vnd.github.v3+json"
            }
          }
        );
        const content = atob(response.data.content);
        this.products = JSON.parse(content);
        this.sha = response.data.sha;
      } catch (error) {
        console.error("Error fetching data:", error);
      }
    },
    async updateData() {
      try {
        const content = btoa(JSON.stringify(this.products, null, 2));
        const response = await axios.put(
          "https://api.github.com/repos/rdmasquesalud/Productos_Store/contents/productos.json",
          {
            message: "Update productos.json",
            content: content,
            sha: this.sha
          },
          {
            headers: {
              Authorization: `Bearer ${this.pat}`,
              Accept: "application/vnd.github.v3+json"
            }
          }
        );
        this.sha = response.data.content.sha;
        alert("Datos actualizados correctamente");
      } catch (error) {
        console.error("Error updating data:", error);
      }
    },
    showAddProduct() {
      this.newProduct.isAdding = true;
    },
    cancelAdd() {
      this.newProduct = {
        name: '',
        price: '',
        descripcion: '',
        CATEGORIA: '',
        Imagen1: '',
        Imagen2: '',
        isAdding: false
      };
    },
    addProduct() {
      this.products.push({
        name: this.newProduct.name,
        price: this.newProduct.price,
        descripcion: this.newProduct.descripcion,
        CATEGORIA: this.newProduct.CATEGORIA,
        Imagen1: this.newProduct.Imagen1,
        Imagen2: this.newProduct.Imagen2
      });
      this.cancelAdd();
    },
    deleteProduct(index) {
      this.products.splice(index, 1);
    }
  }
};
</script>

<style>
/* Agrega tus estilos aquí */
table {
  width: 100%;
  border-collapse: collapse;
}
th, td {
  border: 1px solid #ddd;
  padding: 8px;
}
th {
  background-color: #f2f2f2;
}
tbody tr {
  border-top: 1px solid #ccc;
}
</style>
