<template>
  <div class="container mx-auto px-10 py-10">
    <div class="flex gap-x-5">
      <div class="bg-white px-5 py-3 rounded-md w-full">
        <div>
          <h3 class="text-xl font-semibold text-center">ORIGIN</h3>
          <div class="h-1 rounded-full w-full bg-gray-400 my-2"></div>
        </div>
        <div class="mt-5">
          <label for="" class="block">PROVINCE</label>
          <select @change="getCityOrigin" v-model="state.origin_province" class="bg-gray-200 rounded-md px-3 py-1.5 w-full">
              <option v-for="province in provinces" :key="province.id" :value="province.province_id">
              {{ province.province }}
            </option>
          </select>
        </div>
        <div class="mt-5">
          <label for="" class="block">CITY</label>
          <select v-model="state.origin_city" class="bg-gray-200 rounded-md px-3 py-1.5 w-full">
              <option v-for="city in cities_origin" :key="city.id" :value="city.city_id">
              {{ city.city_name }}
            </option>
          </select>
        </div>
      </div>
      <div class="bg-white px-5 py-3 rounded-md w-full">
        <div>
          <h3 class="text-xl font-semibold text-center">DESTINATION</h3>
          <div class="h-1 rounded-full w-full bg-gray-400 my-2"></div>
        </div>
        <div class="mt-5">
          <label for="" class="block">PROVINCE</label>
          <select @change="getCityDestination" v-model="state.destination_province" class="bg-gray-200 rounded-md px-3 py-1.5 w-full">
              <option v-for="province in provinces" :key="province.id" :value="province.province_id">
              {{ province.province }}
            </option>
          </select>
        </div>
        <div class="mt-5">
          <label for="" class="block">CITY</label>
          <select v-model="state.destination_city" class="bg-gray-200 rounded-md px-3 py-1.5 w-full">
              <option v-for="city in cities_destination" :key="city.id" :value="city.city_id">
              {{ city.city_name }}
            </option>
          </select>
        </div>
      </div>
      <div class="bg-white px-5 py-3 rounded-md w-full">
       <div>
         <h3 class="text-xl font-semibold text-center">KURIR</h3>
          <div class="h-1 rounded-full w-full bg-gray-400 my-2"></div>
       </div>
       <div class="mt-5">
          <label for="" class="block">PILIH KURIR</label>
          <select v-model="state.courier" class="bg-gray-200 rounded-md px-3 py-1.5 w-full">
             <option value="jne">JNE</option>
             <option value="pos">POS</option>
             <option value="tiki">TIKI</option>
          </select>
        </div>
       <div class="mt-5">
          <label for="" class="block">MASUKAN BERAT (GRAM)</label>
          <input type="number" v-model="state.weight" class="bg-gray-200 rounded-md px-3 py-1.5 w-full">
        </div>
      </div>
    </div>
      <div class="my-3">
         <button @click="getCost" class="bg-blue-500 hover:bg-blue-600 rounded-md text-white text-center w-full px-5 py-2">CEK ONGKOS</button>
      </div>

      <div v-if="statusResult != null">
      <div v-if="statusResult" class="bg-white rounded-md px-5 py-3 w-full">
        <div class="content">
          <h3 class="text-center font-semibold">HASIL ONGKIR</h3>
          <div class="h-1 rounded-full w-full bg-gray-400 my-2"></div>
          <div class="mt-5">
            <h3>KURIR : {{ resultCost.results.courier_name }}</h3>
            <div>LAYANAN</div>
            <div v-for="cost in resultCost.results.costs" :key="cost.id">
              {{ cost.description }} - Rp. {{ cost.cost[0]['value'] }} - Estimasi {{ cost.cost[0]['etd'] }} Hari
            </div>
          </div>
        </div>
      </div>
      <div class="bg-white rounded-md px-5 py-3 w-full" v-if="!statusResult">
        <pre>{{ resultCost }}</pre>
      </div></div>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";

export default {
  setup() {
    const provinces = ref({});

    const state = reactive({
      origin_province: "",
      origin_city: "",

      destination_province: "",
      destination_city: "",

      weight: "",
      courier: "",
    });

    const cities_origin = ref({});
    const cities_destination = ref({});

    const resultCost = ref(null);
    const statusResult = ref(null)

    onMounted(() => {
      axios.get("http://127.0.0.1:8000/api/province").then((response) => {
        provinces.value = response.data.results;
      }).catch(error => {
        console.log(error)
      });
    });

    const getCityOrigin = () => {
      axios.get(`http://127.0.0.1:8000/api/city?province=${state.origin_province}`)
      .then(response => {
        cities_origin.value = response.data.results
      })
      .catch(error => {
        console.log(error)
      })
    }

    const getCityDestination = () => {
      axios.get(`http://127.0.0.1:8000/api/city?province=${state.destination_province}`)
      .then(response => {
        cities_destination.value = response.data.results
      })
      .catch(error => {
        console.log(error)
      })
    }

    const getCost = () => {
      axios.post(`http://127.0.0.1:8000/api/ongkir`, {
        origin: state.origin_city,
        destination: state.destination_city,
        weight: state.weight,
        courier: state.courier
      })
      .then(response => {
        if(response.data.status == "200"){
          statusResult.value = true
        } else {
          statusResult.value = false
        }
        resultCost.value = response.data
      })
    }

    return {
      provinces, state, getCityOrigin, cities_origin, getCityDestination, cities_destination, resultCost, getCost, statusResult
    };
  },
};
</script>

<style>
body {
  background: gray;
}
</style>