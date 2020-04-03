<template>
  <div>
    <b-container>
    <b-row>
      <b-col>
        <h2>Products</h2>
      </b-col>
    </b-row>

    <b-row>
      <b-col v-for="product in products" :key="product.id">
        <b-card
          :title  ='product.name'
          :img-src="`@/assets/fruit${product.id}.jpeg`"
          img-alt="Image"
          img-top
          tag="article"
          style="max-width: 20rem;"
          class="mb-2">

          <b-card-text>
            Price:{{product.price}}
          </b-card-text>

          <b-button v-if="!product.cart" @click="add(product)" href="#" variant="primary"> Add to Shopping Cart </b-button>
          <b-button v-if="product.cart" @click="add(product)" :disabled="product.cart" href="#" variant="warning"> Product added to Shopping Cart </b-button>
        </b-card>
      </b-col>
    </b-row>

    <b-row>
      <b-col>
        <h2>Shopping Chart</h2>
      </b-col>
    </b-row>

    <b-row>
      <b-col>
        <b-table bordered hover :items="cart" :fields="fields">
          <template slot="#" slot-scope="data">
            {{ data.index+1 }}
          </template>
          <template slot="price" slot-scope="data">
            {{ data.item.price * data.item.quantity }}
          </template>
          <template slot="remove" slot-scope="data">
            <b-button @click="remove(data.item.id)" variant="danger" class="mr-2">
              x
            </b-button>
          </template>
          <template slot="quantity" slot-scope="data">
            <b-row>
              <b-col cols="5">
                <b-button :disabled="data.item.quantity <=1" variant="primary" @click="decrement(data.item.id)" class="mr-2">
                  -
                </b-button>
              </b-col>
              <b-col cols="2">
                <h4>{{data.item.quantity}}</h4>
              </b-col>
              <b-col cols="5">
                <b-button variant="primary" @click="increment(data.item.id)" class="mr-2">
                  +
                </b-button>
              </b-col>
            </b-row>
          </template>

          <template slot="image" slot-scope="data">
            <b-img style="max-width: 5rem;" :src="`@/assets/fruit${data.item.id}.jpeg`" fluid alt="Responsive Image">
            </b-img>
          </template>
        </b-table>
      </b-col>
    </b-row>
    <b-row v-if="cart.length > 0">
      <b-col></b-col>
      <b-col></b-col>
      <b-col></b-col>
      <b-col><h4>Total</h4></b-col>
      <b-col cols="2"><h4>Rp {{ total }}.00</h4></b-col>
    </b-row>
    <b-row v-if="cart.length > 0">
      <b-col>
        <b-button @click="clean" variant="info" block class="mr-2">
          Clean
        </b-button>
      </b-col>
      <b-col></b-col>
      <b-col cols="4"></b-col>
      <b-col></b-col>
      <b-col>
        <b-button @click="buy" variant="success" block class="mr-2">
          Buy
        </b-button>
      </b-col>
    </b-row>
      <b-modal hide-header-close no-close-on-esc no-close-on-backdrop ref="modal-1" centered title="Purchase Completed">
        <template slot="modal-footer">
          <b-button class="mt-3" variant="info" block @click="clean">
            Close
          </b-button>
        </template>
        <p class="my-4">Products:</p>
        <ul v-for="productFinal in ticket.products" :key="productFinal.id">
          <li>
            Product Name: {{ productFinal.name }}
          </li>
          <li>
            Quantity: {{ productFinal.quantity }}
          </li>
          <li>
            Price: {{ productFinal.price }}
          </li> 
          <li>
            Total: {{ productFinal.price * productFinal.quantity }}
          </li>
          <hr>
        </ul>
        <h2 class="my-4">Total: Rp {{ticket.total}}.00</h2>
      </b-modal>
    </b-container>
  </div>
</template>


<script>
  export default {
    name : 'Cart',
    data(){
      return {
        ticket:{
          products: null,
          total: 0
        },
        counter: 0,
        products: [
            {
              id:1,
              img:'@/assets/fruit1.jpeg',
              name:'Apple',
              price:3000,
              cart:false,
              quantity:1
            },
            {
              id:2,
              img:'@/assets/fruit2.jpeg',
              name:'Orange',
              price:4000,
              cart:false,
              quantity:1
            },
            {
              id:3,
              img:'@/assets/fruits3.jpeg',
              name:'Banana',
              price:5000,
              cart:false,
              quantity:1
            }
        ],
          cart:[],
          fields:['#', 'remove', 'image', 'name', 'quantity', 'price']
      }
    },
    methods: {
          add(product){
            this.products[product.id-1].cart=true
            this.cart.push(product)
            this.counter++
          },
          clean(){
            this.cart=[];

            for (const key in this.products) {
                this.products[key].cart=false
                this.products[key].quantity=1
            }
            this.$refs['modal-1'].hide()
          },
          remove(id) {
            for (let index = 0; index < this.products.length; index++) {
              if (this.products[index].id == id) {
                  this.product[index].cart=false
                  this.product[index].quantity=1
              }
            }
            for (let index = 0; index < this.cart.length; index++) {
              if (this.cart[index].id == id) {
                    this.cart.splice(index,1);
              }
            }
          },
          buy(){
            this.ticket={
              products: this.cart,
              total: this.total
            }
            this.$refs['modal-1'].show()
          },
          increment(id) {
            for (let index=0; index < this.cart.length; index++) {
              if (this.cart[index].id == id) {
                    this.cart[index].quantity++
              }
            } 
          },
          decrement(id){
            for (let index = 0; index < this.cart.length; index++) {
              if (this.cart[index].id == id) {
                    this.cart[index].quantity--
              }
            }
          }
      },
      computed: {
        total(){
          let t = 0;
          for (let index = 0; index < this.cart.length; index++) {
              t += this.cart[index].price*this.cart[index].quantity
          }
          return t
        }
      }
  }

  </script>