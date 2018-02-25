<template>
<div class="hello">
    <section id="searchContainer">
      <h1>Søk i Brønnøysundregisterene</h1>
      <div>
        <input type="number" class="searchBar" id="searchNum" min="9" v-model="inputNum" placeholder="Organisasjonsnummer" />
        <button v-on:click="sendDataNum()">Søk etter org nr</button>
      </div>
      <div>
        <input type="text" class="searchBar" id="searchName" v-model="inputName" placeholder="Bedriftsnavn" />
        <button v-on:click="sendDataName()">Søk etter bedriftsnavn</button>
      </div>
    </section>
    <section id="contentContainer">
          <template v-for="svar in filteredItems">
            <section v-if="svar" class="listItems">
                <p v-if="svar.orgform.beskrivelse" class="itemIndex">{{ svar.orgform.beskrivelse }}</p>
                <h1>{{ svar.navn }}</h1>
                <p>OrgNr: {{svar.organisasjonsnummer}}</p>
                <div v-if="svar.forretningsadresse.kommune">{{ svar.forretningsadresse.poststed }}</div>
                <div v-if="svar.konkurs == 'J'" class="konkurs">BEDRIFTA ER KONKURS</div>
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
import axios from "axios"
var apiUrl = "http://data.brreg.no/enhetsregisteret/enhet/";

export default {
  name: 'HelloWorld',
  data () {
           return {
               items: [],
               inputNum: '',
               inputName: ''
             }
           },

       mounted () {
         axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet.json'}).then(result => {
            this.items = result.data.data
         }, error => {
             console.error(error)
             this.items = 'Det skjedde ein feil...' + error
         });
      },
      computed: {
        filteredItems: function() {
        var that = this
        return this.items.filter(function(svar) {
          return svar.navn.toLowerCase().search(that.inputName.toLowerCase()) !== -1
           //svar.organisasjonsnummer.toString().indexOf(that.inputNum) !== -1
        })
      }
    },
      methods: {
        sendDataNum () {
               axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet/' + this.inputNum }).then(result => {
                  this.items = result.data.data
                  console.log(result.data.data)
               }, error => {
                   console.error(error)
                   this.items = 'Det skjedde ein feil...' + error
               });
           },
        sendDataName () {
                axios({ method: 'GET', 'url': 'http://data.brreg.no/enhetsregisteret/enhet/', 'data.data': this.inputName, 'headers': { 'content-type': 'application/vnd.er-api-v1.0+json' } }).then(result => {
                  this.items = result.data.data
                   }, error => {
                       console.error(error)
                       this.items = 'Det skjedde ein feil...' + error
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
    width: 200px;
    height: 30px;
    font-size: 16px;
  }
  #searchContainer{
    color: black;
    margin: 10px;
    padding: 20px;
  }
  #searchContainer button{
    height: 35px;
    width: 150px;
    background-color: white;
  }
  #searchContainer div{
    display: block;
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
    color: #F55D5D;
    font-weight: strong;
  }
  .itemIndex{
    color: white;
    font-size: 14px;
    font-style: strong;
  }
</style>
