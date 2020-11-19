<template>
    <section id="countries">
      <div v-if="error" class="error">
        <p>{{error.message}}</p>
      </div>
      <div id="countries-box" v-else>
          <AsyncCountryBox v-for="country in countries" :key="country.id" v-show="filter(country)" :country="country" />
      </div>
    </section>
</template>

<script>
//import CountryBox from '@/components/CountryBox.vue'
import {ref, defineAsyncComponent} from "vue";
import Loading from "@/components/Loading.vue";

const AsyncCountryBox = defineAsyncComponent ({
   loader: () => import("./CountryBox.vue" /* webpackChunkName: "CountryBox" */),
   loadingComponent: Loading,
   delay: 200,
   suspensible: false
})
export default {
  async setup () {
    const error = ref(null);
    const countries = ref(null);

    try {
        const countriesResponse = await fetch('https://restcountries.eu/rest/v2/all');
        countries.value = await countriesResponse.json();
        //setTimeout(function(){ console.log('lol') },10000)
    } catch (e) {
        error.value = e;
    }
    return {countries, error};
  },

  name: 'Home',
  props: {
    search: String,
    region: String
  },
  data: function() {
    return {
      /*error: null,
      countries: [],*/
    }
  },
  components: {
    AsyncCountryBox
  },
  mounted() {
    console.log(this.countries)
    //this.consumeAPI();
    /*this.axios.get('https://restcountries.eu/rest/v2/all')
              .then((response) => {(this.countries = response.data); this.error = false})
              .catch(error => this.error = error)*/
  },
  watch: {
  },
  methods: {
    filter: function(country) {
      return ((country.region === this.region || this.region === 'Filter by Region') && country.name.toLowerCase().includes(this.search.toLowerCase()));
    },
    consumeAPI: function() {
        this.axios.get('https://restcountries.eu/rest/v2/all')
        .then((response) => {(this.countries = response.data); this.error = false;})
        .catch(error => {this.error = error} )
    }
  }
}
</script>