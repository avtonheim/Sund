<template>
<div class="hello">
    <section id="searchContainer">
      <img src="../assets/brreg_logo.svg" alt="logo brreg">
      <h1>Søk i enhetsregisteret</h1>
      <p>Søket startar med tredje teikn i søkefeltet med namn, eller med gyldig organisasjonsnummer.</p>
      <form>
        <input type="text" class="searchBar" v-model="input" placeholder="Start eit søk..." />
        <button v-on:click="sendData()">Søk</button>
      </form>
    </section>
    <section id="contentContainer">
          <template v-for="svar in filteredItems">
            <section v-if="svar" class="listItems">
                <p v-if="svar.orgform.beskrivelse" class="itemIndex">{{ svar.orgform.beskrivelse }}</p>
                <h1>{{ svar.navn }}</h1>
                <div v-if="svar.konkurs == 'J'"><p class="konkurs">BEDRIFTA ER KONKURS</p></div>
                <p>OrgNr: {{svar.organisasjonsnummer}}</p>
                <div v-if="svar.forretningsadresse.kommune">Poststed: {{ svar.forretningsadresse.poststed }}</div>
                <div v-if="svar.hjemmeside">{{ svar.hjemmeside }}</div>
                <div v-if="svar.epost">E-post: {{ svar.epost }}</div>
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
    /*Første som skjer då sida lastar inn innhaldet inn i ein arraylist kalla items*/
         axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet.json'}).then(result => {
            this.items = result.data.data
         }, error => {
             console.error(error)
             this.items = 'Det skjedde ein feil...' + error
         });
      },
      computed: {
    /*Søket skal starte på det tredje teiknet, og dersom ein søker på eit org nummer skal dette visast fram*/
        filteredItems: function() {
        var that = this
        return this.items.filter(function(svar) {
          if (that.input.length > 2) {
            var result = svar.navn.toLowerCase().search(that.input.toLowerCase()) !== -1
        } if (isNaN(that.input) === false && that.input.length === 9){
            var result = svar.organisasjonsnummer.toString().indexOf(that.input) !== -1
         } return result
        })
      }
    },
      methods: {
      /*Kaller på api-et når ein skal søke etter namn og org nr*/
      /*Den fungerer ikkje som eg vil!*/
        sendData () {
               axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet/' + this.input + '.json'}).then(result => {
                 this.items = result.data
                 console.log(result.data)
               }, error => {
                   console.error(error)
                   that.items = 'Det skjedde ein feil...' + error
               });
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
    width: 150px;
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
    font-weight: strong;
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
