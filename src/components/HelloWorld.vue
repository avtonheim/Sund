<template>
<div class="hello">
      <h1>Your IP is {{ ip }}</h1>
      <input type="text" v-model="input.companyName" placeholder="Bedriftsnavn" />
      <input type="integer" v-model="input.companyOrg" placeholder="Organisasjonsnummer" />
      <button v-on:click="sendData()">Send</button>
      <br />
      <br />
      <textarea>{{ response }}</textarea>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'HelloWorld',
  data () {
           return {
               ip: "",
               input: {
                   companyName: "",
                   companyOrg: ""
               },
               response: ""
           }
       },
       mounted() {
       axios({ method: "GET", "url": "http://data.brreg.no/enhetsregisteret/enhet.json" }).then(result => {
                this.response = result.data;
            }, error => {
                console.error(error);
            });
      },
       methods: {
       sendData() {
               axios({ method: "POST", "url": "http://data.brreg.no/enhetsregisteret/enhet/", "data": this.input, "headers": { "content-type": "application/json" } }).then(result => {
                   this.response = result.data;
               }, error => {
                   console.error(error);
               });
           }
       }
   }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
      font-weight: normal;
  }

  ul {
      list-style-type: none;
      padding: 0;
  }

  li {
      display: inline-block;
      margin: 0 10px;
  }

  a {
      color: #42b983;
  }
  textarea {
      width: 600px;
      height: 200px;
  }
</style>
