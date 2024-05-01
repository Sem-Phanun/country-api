<template>
    <div class="py-4">
            <form class="flex justify-center items-center">
                <label class="text-lg font-bold mb-2 mx-2" for="search-input">Search:</label>
                <input type="search" id="search-input" class="p-2 pl-10 w-[20rem] text-lg border border-gray-400 rounded focus:outline-none"
                    v-model="searchQuery"
                >
            </form>
    </div>
    <button
        class="mx-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="previousPage" :disabled="currentPage === 1"
    >Previous</button>
    <span>{{ currentPage }} / {{ totalPages }}</span>
    <button
        class="mx-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="nextPage" :disabled="currentPage === totalPages"
    >Next</button>
    <div>
        <h1 class="text-center text-4xl">List of country</h1>
    </div>
    <div class="w-[90%] m-auto flex flex-wrap gap-4">
        <div
            class="relative flex flex-col px-4 mt-6 text-gray-700 bg-slate-300 shadow-md bg-clip-border rounded-xl w-96"
            v-for="(item, index) in paginatedCountries"
            :key="index"
        >
            <div
                class="relative h-48 mx-4 mt-4 overflow-hidden text-white shadow-lg bg-clip-border rounded-xl bg-blue-gray-500 shadow-blue-gray-500/40"
            >
                <img :src="item.flags.png" class="w-[100%] h-[100%]" alt="card-image" />
            </div>
            <div class="p-6">
                <h5
                    class="cursor-pointer block mb-2 font-sans text-xl antialiased font-semibold leading-snug tracking-normal text-blue-gray-900"
                >
                    {{ item.name.official }}
                </h5>
                <p
                    class="block font-sans text-base antialiased font-light leading-relaxed text-inherit"
                >
                    cca2:    {{ item.cca2 }}
                </p>
                <p
                    class="block font-sans text-base antialiased font-light leading-relaxed text-inherit"
                >
                cca3: {{ item.cca3 }}
                </p>
                <p
                    class="block font-sans text-base antialiased font-light leading-relaxed text-inherit"
                    v-for="(nativename, nativenameKey) in item.name.nativeName" :key="nativenameKey"
                >
                    <span>{{ nativenameKey }} : </span>
                    <li v-for="(name, index) in nativename" :key="index">
                        {{ index }} : {{ name }}
                    </li>
                </p>
                <p class="block font-sans text-base antialiased font-light leading-relaxed text-inherit">
                    <span>root:</span> 
                    <span>{{ item.idd.root }}</span>
                    <!-- <span class="text-left ml-16">toogle</span> -->
                    <li>suffixes:</li>
                    <ul v-for="(suffixed, index) in item.idd.suffixes" :key="index"
                        class="mx-14"
                    >
                        
                        <li>{{ suffixed }}</li>
                    </ul>
                </p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import axios from "axios";

const countries = ref([]);
const searchQuery = ref('');
const rowsPerPage = 25
const currentPage = ref(1)

onMounted(() => {
  getAllCountry();
});

//get all country data 
const getAllCountry = async () => {
  try {
    const baseUri = await axios.get("https://restcountries.com/v3.1/all");
    countries.value = baseUri.data;
  } catch (error) {
    console.log(error);
  }
};

//implement fuzzy search
const helperQuery = (query, somethinglike) => {
    let i = 0;
    let j = 0;
    while (i < query.length && j < somethinglike.length) {
      if (query[i] === somethinglike   [j]) {
        i++;
      }
      j++;
    }
    return i === query.length;
};

//filter countries 
const searchCountry = computed(()=> {
    return countries.value.filter(country => {
        return helperQuery(searchQuery.value.toLowerCase(), country.name.official.toLowerCase())
    })
})


//pagination 
const paginatedCountries = computed (()=> {
    const startIndex = (currentPage.value - 1) * rowsPerPage
    const endIndex = startIndex + rowsPerPage
    return searchCountry.value.slice(startIndex, endIndex)
})

const totalPages = computed(()=> {
    return Math.ceil(searchCountry.value.length / rowsPerPage)
})

// Previous page function
const previousPage = () => {
    if (currentPage.value > 1) {
        currentPage.value--
    }
}
const nextPage = () => {
    if (currentPage.value < totalPages.value) {
        currentPage.value++
    }
}

</script>

