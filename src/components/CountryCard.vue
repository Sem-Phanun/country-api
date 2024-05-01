<template>
    <div class="py-4">
        <form class="flex justify-center items-center">
            <label class="text-lg font-bold mb-2 mx-2" for="search-input">Search:</label>
            <input type="search" id="search-input"
                class="p-2 pl-2 w-[20rem] text-lg border border-gray-400 rounded focus:outline-none"
                v-model="searchQuery"
                placeholder="search country"    
            >
        </form>
    </div>
    <div class="flex items-center">
        <div>
            <button class="mx-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
                @click="previousPage" :disabled="currentPage === 1">Previous</button>
            <span>{{ currentPage }} / {{ totalPages }}</span>
            <button class="mx-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" @click="nextPage"
                :disabled="currentPage === totalPages">Next</button>
        </div>
        <div> 
            sorting name by: 
            <select
                v-model="sortingType"
                class="bg-blue-600 outline-0 border text-white py-2 px-2 cursor-pointer">
                <option value="asc" class="bg-blue-300">ASC</option>
                <option value="desc" class="bg-blue-300">DESC</option>
            </select>
        </div>
        <div>
            <h4 class="absolute right-2">Total Page: {{ totalPages }}</h4>
        </div>
    </div>
    <div>
        <h1 class="text-center text-4xl">List of country</h1>
    </div>
    <div class="w-[90%] m-auto">
        <ul class="divide-y divide-gray-300">
            <li v-for="(item, index) in paginatedCountries" :key="index" class="py-4">
                <div class="flex items-center">
                    <div class="mx-4">
                        {{ index +1 }}
                    </div>
                    <div class="w-24 h-16 flex-shrink-0">
                        <img :src="item.flags.png" class="w-full h-full" alt="Flag">
                    </div>
                    <div class="ml-4">
                        <span class="uppercase font-bold">official name: </span>
                        <span class="text-2xl font-semibold text-blue-gray-900 cursor-pointer">{{ item.name.official }}</span>
                        <div>
                            
                            <li class="text-sm text-gray-700"> 
                                <span class="uppercase font-bold">cca2: </span>
                                {{ item.cca2 }}
                            </li>
                            
                            <li class="text-sm text-gray-700">
                                <span class="uppercase font-bold">cca3: </span>
                                {{ item.cca3 }}
                            </li>
                        </div>
                        <ul>
                            <li v-for="(nativename, nativenameKey) in item.name.nativeName" :key="nativenameKey"
                                class="text-sm text-gray-700">
                                <span class="uppercase font-bold">{{ nativenameKey }}: </span>
                                <li v-for="(name, index) in nativename" :key="index"
                                    class="px-8"
                                >
                                 {{ index }}: {{ name }}
                                </li>
                            </li>
                        </ul>
                        <ul>
                            <span class="uppercase font-bold">alt: </span> 
                            <li v-for="(alt, index) in item.altSpellings" :key="index"
                                class="px-8"
                            > {{ alt}}</li>
                        </ul>
                        <p class="text-sm text-gray-700">
                            <span>Root:</span> {{ item.idd.root }}
                            
                        <ul>
                            <span>Suffixes:</span>
                            <li v-for="(suffixed, index) in item.idd.suffixes" :key="index"
                                class="text-sm text-gray-700 mx-14">
                                {{ suffixed }}
                            </li>
                        </ul>
                        </p>
                    </div>
                </div>
            </li>
        </ul>
    </div>

</template>

<script setup>
import { ref, onMounted, computed, watch } from "vue";
import axios from "axios";

const countries = ref([]);
const searchQuery = ref('');
const rowsPerPage = 25
const currentPage = ref(1)
const sortingType= ref('')

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
        if (query[i] === somethinglike[j]) {
            i++;
        }
        j++;
    }
    return i === query.length;
};

//filter countries 
const searchCountry = computed(() => {
    return countries.value.filter(country => {
        return helperQuery(searchQuery.value.toLowerCase(), country.name.official.toLowerCase())
    })
})


//pagination 
const paginatedCountries = computed(() => {
    const startIndex = (currentPage.value - 1) * rowsPerPage
    const endIndex = startIndex + rowsPerPage
    return searchCountry.value.slice(startIndex, endIndex)
})

const totalPages = computed(() => {
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

//watcher 
watch(sortingType, (newSortingType, oldSortingType)=> {
    if(newSortingType == 'asc') {
        countries.value.sort(sortCountryNameByAsc)
        console.log( 'sort by asc', countries.value.sort(sortCountryNameByAsc))
    }else if (newSortingType == 'desc'){
        countries.value.sort(sortCountryNameByDesc)
        console.log('sort by desc', countries.value.sort(sortCountryNameByDesc))
    }
})

//implement sort 
const sortCountryNameByAsc = (asc, desc)=>{
    return asc.name.official.localeCompare(desc.name.official)
} 
const sortCountryNameByDesc = (asc, desc) => {
    return desc.name.official.localeCompare(asc.name.official)
}
</script>
