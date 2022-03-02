<template>
  <div id="app" class="flex justify-center">
    <div v-if="errorArray.length" v-on:click="closeAllPopups" class="top-1/4 absolute w-full h-full flex justify-center">
      <div role="alert" class="m-5 w-10/12 absolute">
        <div class="bg-red-500 text-white font-bold rounded-t px-4 py-2">
          Error
        </div>
        <div class="border border-t-0 border-red-400 rounded-b bg-red-100 px-4 py-3 text-red-700">
          <div v-for="error in errorArray"  :key="error.id">
            {{error}}
          </div>
        </div>
      </div>
    </div>
    <div v-if="IsGameOver" v-on:click="closeAllPopups" class="absolute w-full h-full flex justify-center">
      <div class="w-10/12 top-1/4 absolute flex items-center justify-center bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded" role="alert">
        <div class="absolute top-0 right-2">X</div>
        YOU DIED <span class="text-4xl ml-3">&#128546;</span>
      </div>
    </div>
    <div v-if="IsWin" v-on:click="closeAllPopups" class="absolute w-full h-full flex justify-center">
      <div class="w-10/12 top-1/4 absolute flex items-center justify-center bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded" role="alert">
        <div class="absolute top-0 right-2">X</div>
        YOU WIN <span class="text-4xl ml-3">&#128512;</span>
      </div>
    </div>
    <div v-if="IsBadAnswer" v-on:click="closeAllPopups" class="absolute w-full h-full flex justify-center">
      <div class="w-10/12 top-1/4 absolute flex items-center justify-center bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded" role="alert">
        <div class="absolute top-0 right-2">X</div>
        That was wrong, you lost a life. Please try again. <span class="text-4xl ml-3">&#128546;</span>
      </div>
    </div>
    <div v-if="IsGoodAnswer" v-on:click="closeAllPopups" class="absolute w-full h-full flex justify-center">
      <div class="w-10/12 top-1/4 absolute flex items-center justify-center bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded" role="alert">
        <div class="absolute top-0 right-2">X</div>
        Congratulations, you selected a correct match. <span class="text-4xl ml-3">&#128512;</span>
      </div>
    </div>
    <div class="w-10/12">
      <section>
        <div class="mb-5">
          <span class="border border-t-0 border-slate-300 bg-blue-300 p-2 rounded-b-xl text-2xl">
            lives: {{Lives}}
          </span>
        </div>
        <form v-on:submit.prevent="">
          <div class="flex justify-center grid grid-cols-6 gap-2" v-if="WordArray.length===7 && DefinitionArray.length===7">
            <div>
              <div v-for="word in WordArray" :key="word.id"
                   :class="[word.disabled? 'bg-green-300' : 'bg-slate-100', {'bg-blue-400': word.id == WordSelection.id}]"
                   class="border border-slate-300 p-1 m-2 rounded">
                <label class="block">
                  <input class="hidden" type="radio" :disabled="word.disabled" v-model="WordSelection" :value="word" id="word.id" name="word-select"/>
                  {{word.value}}
                </label>
              </div>
            </div>
            <div>
              <div v-for="definition in DefinitionArray" :key="definition.id" class="m-2">
              </div>
            </div>
            <div class="col-span-4">
              <div v-for="definition in DefinitionArray" :key="definition.id"
                   :class="[definition.disabled? 'bg-green-300' : 'bg-slate-100', {'bg-blue-400': definition.id == DefinitionSelection.id}]"
                   class="border border-slate-300 p-1  m-2 rounded">
                    <label class="block">
                      <input class="hidden" type="radio" :disabled="definition.disabled" v-model="DefinitionSelection" :value="definition" id="definition.id" name="definition-select"/>
                      {{definition.value}}
                    </label>
              </div>
            </div>
          </div>
          <div class="flex justify-center">
            <button :disabled="IsGameOver" type="submit" v-on:click="submitForm" class="bg-blue-500 font-bold hover:bg-blue-700 mt-10 px-4 py-2 rounded text-white w-10/12">Submit</button>
          </div>
        </form>
      </section>
      <section class="mt-10">
        <h1>Found:</h1>
        <ul>
          <li v-for="words in FoundWords" :key="words.id" class="border border-slate-300 bg-slate-100 p-1  m-2 rounded">
            {{words.word}} : {{words.definition}}
          </li>
        </ul>
      </section>
    </div>
  </div>
</template>


<script>
export default {
  name: 'App',
  components: {
  },
  data(){
    return {
      errorArray: [],
      IsGameOver: false,
      IsWin: false,
      IsGoodAnswer: false,
      IsBadAnswer: false,
      Lives: 10,
      WordArray: [],
      DefinitionArray: [],
      WordSelection: {},
      DefinitionSelection: {},
      FoundWords: []
    }
  },
  watch: {
    Lives: function () {
      if(this.lives === 0 ){
        this.IsGameOver = true;
      }
      this.IsGameOver = false;
    },
  },
  methods:{
    isWin: function(){
      for (let i=0; i<this.WordArray.length; i++){
        if(this.WordArray[i].disabled === false){
          return false;
        }
      }
      return true;
    },
    closeAllPopups: function(){
      this.IsGoodAnswer = false;
      this.IsBadAnswer = false;
      this.IsGameOver = false;
      this.IsWin = false;
    },
    shuffleArray: function (array) {
        array.sort(() => Math.random() - 0.5);
    },
    submitForm: function() {
      if(this.WordSelection.id !== this.DefinitionSelection.id){
        this.IsBadAnswer = true;
        this.Lives -= 1;
        return;
      }
      if(this.isWin()){
        this.IsWin = true;
      }
      this.FoundWords.push({
        id: this.WordSelection.id,
        word: this.WordSelection.value,
        definition: this.DefinitionSelection.value
      });
      this.IsGoodAnswer = true;
      this.WordSelection.disabled = true;
      this.DefinitionSelection.disabled = true;
      this.WordSelection = {};
      this.DefinitionSelection = {};
    }
  },
  created: function () {
    const axios = require('axios');
    let url = 'https://random-words-api.vercel.app/word';
    for (let i=0; i<7; i++) {
      setTimeout(
        axios.get(url)
          .then(response => {
            let data = response.data[0];
            this.WordArray.push({
              id: i,
              value: data.word,
              disabled: false
            });
            this.DefinitionArray.push({
              id: i,
              value: data.definition,
              disabled: false
            });
            this.shuffleArray(this.DefinitionArray);
          })
          .catch(error => {
            this.errorArray.push(error);
          })
        ,100
      );
    }
    this.DefinitionArray = this.DefinitionArray.sort(() => Math.random() - 0.5)
  }
}
</script>