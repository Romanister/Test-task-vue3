<template>
  <div class="wrapper" id="wrapper">
    <div class="container">
      <h3>Запрос:</h3> 
      <input 
      type="text"
      v-model="this.name"
      placeholder="Введите запрос"
      @input="changeBrand"
      >
    </div>
    <div class="container">
      <h3>Мой бренд:</h3> 
      <input 
      type="text"
      v-model="this.brand"
      placeholder="Введите бренд"
      >
    </div>
    <button class="btn" @click="findBrand">Поиск</button>
    <h1>Рейтинг по запросу: "{{ this.name }}"</h1>
    <div class="brands-list" v-for="(brand, index) in this.brands" :id="brand.name">
        {{ index += 1 }}. {{ brand.name }}  
    </div>
  </div>
</template>

<script>
import axios from "axios";
import debounce from "lodash/debounce"
export default {
  data(){
    return {
      name:"",
      brand:"",
      brands:[],
    }
  },
  methods: {
    findBrand(){
      for(let i=0; i < this.brands.length; i++){
         if (this.brands[i].name == this.brand){
          let elem = document.getElementById(this.brands[i].name);
          elem.style.color = 'red';    
        }
        else {
          let elem = document.getElementById(this.brands[i].name);
          elem.style.color = 'black';
        }
      }
    },
    async getBrands(){
      this.brands = await axios.get(`https://search.wb.ru/exactmatch/ru/common/v4/search?filters=fbrand&query=${this.name}&resultset=filters&suppressSpellcheck=false&spp=0&regions=80,64,83,4,38,33,70,69,86,30,40,48,1,66,31,22,114&pricemarginCoeff=1.0&reg=0&appType=1&emp=0&locale=ru&lang=ru&curr=rub&couponsGeo=2,12,7,3,6,21,16&dest=-1221148,-140294,-1751445,-364763`)
      this.brands = this.brands.data.data.filters[0].items
      console.log('done')
    }
  },
  created() {
    this.changeBrand = debounce(event => {
      this.getBrands();
    }, 1000);
  },
  beforeUnmount() {
    this.changeBrand.cancel();
  }

}
</script>

<style lang="scss">
#app {
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
.wrapper {
  display: flex;
  flex-direction: column;
  gap: 20px;
  padding-left: 10px;
}
.container {
  display: flex;
  gap: 20px;
}
.container h3 {
  width: 120px;
  font-weight: 600;
  font-size: 22px;
}
.container input {
  outline: none;
  height: 35px;
  width: 350px;
  font-size: 20px;
}
.btn {
  width: 300px;
  height: 30px;
}
.brands-list {
  font-size: 20px;
  font-weight: 700;
}
}
</style>
