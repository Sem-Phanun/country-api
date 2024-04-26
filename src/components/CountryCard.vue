<template>
    <div class="w-[90%] m-auto flex flex-wrap gap-4">
        <div
            class="relative flex flex-col px-4 mt-6 text-gray-700 bg-slate-300 shadow-md bg-clip-border rounded-xl w-96"
            v-for="(item, index) in countries"
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
                    <span v-for="(name, index) in nativename" :key="index">
                        {{ index }} : {{ name }}
                    </span>
                </p>
                <p class="block font-sans text-base antialiased font-light leading-relaxed text-inherit">
                    <span>root:</span> 
                    <span>{{ item.idd.root }}</span>
                    <span class="text-left ml-16">toogle</span>
                    <ul v-for="(suffixed, index) in item.idd.suffixes" :key="index">
                        <li>{{ suffixed }}</li>
                    </ul>
                </p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const countries = ref([]);

onMounted(() => {
  getAllCountry();
});
const getAllCountry = async () => {
  try {
    const baseUri = await axios.get("https://restcountries.com/v3.1/all");

    countries.value = baseUri.data;
    console.log(baseUri.data);
  } catch (error) {
    console.log(error);
  }
};
</script>

<style lang="scss" scoped></style>
