<template>
<div class="hello">
    <section id="searchContainer">
      <img src="../assets/brreg_logo.svg" alt="logo brreg">
      <h1>Søk i enhetsregisteret</h1>
      <p>Søket startar med tredje teikn i søkefeltet med namn, eller med gyldig organisasjonsnummer (ni siffer).</p>
        <input type="text" class="searchBar" v-model="input" placeholder="Start eit søk..." />
        <button v-on:click="handleInput()">Søk i databasen</button>
    </section>
    <section id="contentContainer">
      <h2 v-if="input">Søkeresultat</h2>
          <template v-for="svar in filteredItems">
            <section v-on:click="showDetails()" class="listItems" v-if="svar">
                <p v-if="svar.orgform.beskrivelse" class="itemIndex">{{ svar.orgform.beskrivelse }}</p>
                <h1>{{ svar.navn }}</h1>
                <div v-if="svar.konkurs == 'J'"><p class="konkurs">BEDRIFTA ER KONKURS</p></div>
                <p>OrgNr: {{svar.organisasjonsnummer}}</p>
                <div v-if="svar.hjemmeside">{{ svar.hjemmeside }}</div>
                <div v-if="svar.epost">{{ svar.epost }}</div>
          </section>
        </template>

    </section>
    <section>

    </section>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'MainPage',
  data () {
           return {
               items: [],
               input: ''
             }
           },

       mounted () {
    /* Loads when the page loads: this loads the first hundred items from the api into an arraylist called items */
         axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet.json'}).then(result => {
            this.items = result.data.data
         }, error => {
             console.error(error)
             this.items = 'Det skjedde ein feil...' + error
         });
      },
      computed: {
    /* This checks if the input is longer than two characters to search for entity name incrementally,
    or if the user search for a valid org number (nine digits). */
        filteredItems: function() {
        var that = this
        return that.items.filter(function(svar) {
          if (that.input.length > 2) {
            var result = svar.navn.toLowerCase().search(that.input.toLowerCase()) !== -1
        } if (isNaN(that.input) == false && that.input.length == 9){
            var result = svar.organisasjonsnummer.toString().search(that.input) !== -1
         } return result
        })
      }
    },
      methods: {
        /* Handles input based on type of input, isNaN() checks if the input is a number or not */
          handleInput () {
            if (isNaN(this.input) == false && this.input.length == 9){
              /*Calls the api to search for a valid org number*/
              axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet/' + this.input + '.json'}).then(result => {
                this.items = result.data
                this.org = result.data
              }, error => {
                  console.error(error)
                  that.items = 'Det skjedde ein feil...' + error
              });
            } else {
            /*Calls the api to search for name that starts with user input*/
              axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet.json?$filter=startswith%28navn%2C%27' + this.input + '%27%29'}).then(result => {
                this.items = result.data.data
              }, error => {
                  console.error(error)
                  that.items = 'Det skjedde ein feil...' + error
              });
          }
        },
        showDetails ($event) {
          console.log('click!');
        }
       }
   }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
      font-weight: strong;
  }
  a {
      color: #42b983;
  }
  .searchBar{
    width: 300px;
    height: 60px;
    font-size: 26px;
  }
  #searchContainer{
    color: black;
    margin: 10px;
    padding: 20px;
    background-color: #ADEFFF;
    border-bottom: solid 20px #6CE3FF;
  }
  #searchContainer button{
    height: 65px;
    width: 250px;
    background-color: white;
    font-size: 26px;
  }
  #contentContainer{
    width: 100%;
  }
  .listItems{
    display: inline-grid;
    color: black;
    font-size: 20px;
    border-bottom: solid 5px;
    width: 250px;
    min-height: 300px;
    margin: 10px;
    padding: 20px;
    background-color: #6CE3FF;
    text-align: left;
    line-height: 1.5em;
  }
  .konkurs{
    color: #D64E4E;
    font-weight: 900;
  }
  .itemIndex{
    color: white;
    font-size: 14px;
    font-weight: strong;
  }
  img {
    width: 40%
  }
</style>
