<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <div>{{a}}</div>
    <button v-debounce="1000" @click="changeA">click</button>
    <input v-throttle="1000" v-model="inputValue" @input="inputChange"/>
    <textarea v-debounce />
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  data(){
    return {
      a: "54321",
      inputValue: "123"
    }
  },
  methods:{
    changeA(){
      console.log("button click");
      this.a = this.a.split("").reverse().join("");
    },
    inputChange(){
      console.log(this.inputValue, 'input value')
    },
    inputChanged(){
      console.log('????????????');
    }
  },
  components: {
    HelloWorld
  },
  directives: {
    focus: {
      inserted: function(el){
        el.focus();
      },
    },
    debounce: {
      bind: function(el, binding){
        console.log(typeof binding.value)
        if(typeof binding.value !== 'number' && binding.value !== undefined){
          throw Error("debounce value must be number");
        }
        let debounceTime = binding.value || 2000;
        let timer = null;
        let virtualHandle = false
        let evtName;
        if(el.nodeName !== "INPUT" && el.nodeName !== "TEXTAREA"){
          evtName = "click"
        }else{
          evtName = 'input'
        }
        el.addEventListener(evtName, function(event){
          if(timer)clearTimeout(timer)
          if(!virtualHandle){
            event && event.stopImmediatePropagation();
            timer = setTimeout(()=>{
              virtualHandle = true
              if(evtName === 'input'){
                let evt = new InputEvent('input')
                el.dispatchEvent(evt)
              }else{
                el.click();
              }
              setTimeout(()=>{
                virtualHandle = false;
              },0)
            },debounceTime)
          }
        },true)
      }
    },
    throttle:{
      bind: function(el, binding){
        let timer = null;
        let throttleTime;
        let virtualHandle = false;
        let evtName;
        let firstExc = false;
        if(Object.prototype.toString.call(binding.value) === '[object Object]'){
          throttleTime = binding.value.timer || 2000
          firstExc = binding.value.firstExc === undefined ? false : binding.value.firstExc
        }
        if(typeof binding.value === 'number'){
          throttleTime = binding.value || 2000;
        }
        if(el.nodeName !== "INPUT" && el.nodeName !== "TEXTAREA"){
          evtName = "click"
        }else{
          evtName = 'input'
        }
        if(!firstExc){
          el.addEventListener(evtName, function(event){
            if(!virtualHandle){
              event && event.stopImmediatePropagation();
              if(timer) return;
              timer = setTimeout(()=>{
                virtualHandle = true;
                timer = null;
                if(evtName === 'input'){
                  let evt = new InputEvent('input')
                  el.dispatchEvent(evt);
                }else{
                  el.click();
                }
                setTimeout(()=>{
                  virtualHandle = false;
                },0)
              },throttleTime)
            }
          },true)
        }else {
          let lastTime = 0;
          let now = 0;
          el.addEventListener(evtName, function(event){
            now = new Date().getTime();
            console.log(now - lastTime);
            if(now -lastTime < throttleTime){
              event && event.stopImmediatePropagation();
            }else{
              lastTime = now;
            }
            if(lastTime === 0)lastTime = now;
          },true)
        }
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
