<script setup>
import { ref, computed, watch } from 'vue'
const catList = ref([]);

fetch('/data/cats.json')
  .then((res) => res.json())
  .then((data) => {
    console.log(data);
    catList.value = data.cats;
  });

const cart = ref([]);

const totalPrice = ref(0);
watch(cart, () => {
  let total = 0;
  for (let i = 0; i < cart.value.length; i++) {
    total += cart.value[i].price * cart.value[i].qty;
  }
  totalPrice.value = total;
}, { deep: true}); 

// const totalPrice = computed(() => {
//   let total = 0;
//   for (let i = 0; i < cart.value.length; i++) {
//     total += cart.value[i].price * cart.value[i].qty;
//   }
//   return total;
// });

const addToCart = (cats) => {
  const itemIdx = cart.value.findIndex(d => d.name == cats.name);
  if (itemIdx == -1) {
    cart.value.push({
      name: cats.name,
      price: cats.price,
      qty: 1
    });
  }
  else {
    cart.value[itemIdx].qty++;
  }
}

const removeFromCart = (cats) => {
  cart.value = cart.value.filter(d => d.name != cats.name);
}

</script>

<template>
  <main>
    <header>
      <nav class="shadow navbar bg-base-100">
        <div class="flex-1">
          <a class="text-xl btn btn-ghost"><i class="fas fa-gem"></i> 賺很大商店</a>
        </div>
        <div class="flex-none">
          <ul class="px-1 menu menu-horizontal">
            <li>
              <a href="https://github.com/5xTraining/shopping-cat-v3" target="_blank"><i
                  class="fa-brands fa-github"></i> GitHub</a>
            </li>
          </ul>
        </div>
      </nav>
    </header>

    <section class="container px-6 py-3 mx-auto">
      <p class="px-6 py-2 bg-yellow-400 rounded-full">請以認養取代購買！</p>
      <div class="grid grid-cols-1 gap-3 my-2 lg:grid-cols-6 sm:grid-cols-3">
        <!-- cat start -->
        <div v-for="cats in catList" class="shadow card bg-base-100">
          <figure>
            <img v-bind:src="`/images/${cats.picture}`" class="select-none" alt="" />
          </figure>
          <div class="card-body">
            <h5 class="card-title">{{ cats.name }}</h5>
            <p>${{ cats.price }}</p>
            <div class="justify-end card-actions">
              <button @click="addToCart(cats)" class="btn btn-primary">
                <i class="fas fa-cat"></i> 認養
              </button>
            </div>
          </div>
        </div>
        <!-- cat end -->

      </div>

      <section class="px-8 mt-12">
        <h2 class="text-3xl font-bold">認養清單</h2>

        <p v-if="cart.length == 0">目前沒有認養的貓咪</p>

        <table v-else class="table my-2">
          <thead>
            <tr class="text-lg">
              <th>名字</th>
              <th>數量</th>
              <th>手續費</th>
              <th>小計</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <!-- item start -->
            <tr v-for="cats in cart" class="text-lg">
              <td>{{ cats.name }}</td>
              <td>
                <input type="number" v-model="cats.qty" min="1" class="input input-bordered" />
              </td>
              <td>${{ cats.price }}</td>
              <td>${{ cats.price * cats.qty }}</td>
              <td>
                <button @click="removeFromCart(cats)" class="btn btn-xs btn-primary">
                  <i class="fas fa-trash-alt"></i>
                </button>
              </td>
            </tr>
            <!-- item end -->

          </tbody>
          <tfoot class="text-lg bg-slate-100">
            <tr>
              <td colspan="2"></td>
              <td>總價</td>
              <td class="font-bold">{{ totalPrice }}</td>
              <td></td>
            </tr>
          </tfoot>
        </table>
        <button @click="cart = []" class="btn btn-primary">
          <i class="fas fa-baby-carriage"></i> 清空認養清單
        </button>
      </section>
    </section>
  </main>
</template>
