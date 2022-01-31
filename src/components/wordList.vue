<template>
    <div class="container  mx-auto py-14" > 
      <div class="flex mx-64 mb-4">
        <div class="flex justify-start w-full">
        <div class="bg-white border-4	border-orange-500	 rounded-full drop-shadow-2xl p-4 text-3xl font-black flex items-center justify-center  w-24 h-24">{{time}}</div>
        </div>
        <div class="flex justify-end">
            <div class="bg-white border-4	border-green-500 mr-2	 rounded-lg drop-shadow-2xl p-4 text-3xl font-black flex items-center justify-center  w-24 h-24">{{point.correct.length}}</div>
            <div class="bg-white border-4	border-red-500	 rounded-lg drop-shadow-2xl p-4 text-3xl font-black flex items-center justify-center  w-24 h-24">{{point.wrong.length}}</div>

        </div>
      </div>

      <div @click="startFocus" class="flex mx-64 bg-white rounded-lg drop-shadow-2xl relative text-4xl flex leading-[4.375]">
        <div  class="w-1/2 flex justify-end font-semibold overflow-hidden">
        <span  class="pr-2" v-for="(textlist ,index) in text" :key="index" :class="(textlist.status) ? 'text-green-600' : 'text-red-600' "  :data-id="index">{{textlist.string}}</span>
          <div style="float: right;">
            <div class="text-blue-600 text-right whitespace-nowrap flex border-0 w-[5px] font-semibold" 
            
            @keydown.space.prevent="inputModel" 
            @keydown.enter.prevent="inputModel"   
            @keydown.enter.shift.exact.prevent="false"
            @keydown.ctrl.exact.prevent="false"
            @input.prevent="getValueInput"
            v-focus 
            tabindex="1" ref="typeBox" :contenteditable="content" autocomplete="off" autocorrect="off" autocapitalize="off" spellcheck="false">
            </div>
          </div>
        </div>
        
        <div class="w-1/2 overflow-hidden flex font-semibold">
            <span class="pr-2" v-for="(data ,index) in datas" :key="index" :data-id="index">{{(index==0)? textLastArr : data}}</span>
          </div>
      </div>

      <KeyboardVue/>
    </div>
</template>

<script>
 import json from '../assets/data.json'
 import eventBus from './eventBus'
 import KeyboardVue from './Keyboard.vue'
 
  export default {
 
    data(){
      return {
  
        textValue : [],
        charValue : null,
        time: 60,
        point : {correct : [], wrong: []},
        control : null,
        start : false,
        datas : [],
        text : [],
        items : 200,
        count : 0,
        content:true,
        finish : false,
        playAgain: false,
      
  }
    },
    methods: {

    randomNumber : function(){
        let numArr = [];
        for (let i = 0; i < this.items; i++) {
          let a = true,
              n;
          while(a) {
            n = Math.floor(Math.random() * json.length) << 0;
            a = numArr.includes(n);
          }
          numArr.push(n);
        }
        return numArr;
      },
      SetData : function(){
        let setdata = [];
        this.randomNumber().forEach(e=> {
          setdata.push(json[e]);
        });

        this.datas = setdata;
      },
      inputModel : function(e){
      
        if(this.time>0){
              const arr =  e.target.innerHTML.split('&nbsp;');
          var say = arr.length;
          const num = say-1;
          if(arr[num]){
          
            if(arr[num]==this.datas[0]){
              this.text.push({string:arr[num],status:true});
              this.point.correct.push(arr[num]);
            
            }else {
              this.text.push({string:arr[num],status:false});
              this.point.wrong.push(arr[num]);
            }
            
            
            this.datas.splice(0, 1);
          }
          e.target.innerHTML = null;
        }else {
          return false;
        }
        
        
      },
      startFocus : function(){
        this.$refs.typeBox.focus();
      },
      removeByIndex: function(){
      return this.charValue = this.datas[0].substring(0,this.count);
      
      },
      getValueInput : function(e){
    
        var arr = e.target.innerHTML.split('&nbsp;');
        this.control = arr[arr.length-1];
        
      },
      timeDown : function(){
        const timer = setInterval(() => {
          this.time-=1;

          if (this.time == 0) {
            clearInterval(timer)
          }
      }, 1000);

      return timer;
      },

    },
    watch: {
      text: function(){
  
        eventBus.$emit('count',0);
        eventBus.$emit('addword',this.text);
        eventBus.$emit('point',this.point);
        this.textValue = [];
    
      },
      control : function(){
        this.start = true;
        this.removeByIndex();
        let test = this.datas[0];
        var bak = test.substring(this.count);

        if(this.control==this.charValue){
          this.textValue.push(bak);
        }

      },start : {
          handler(value){
            if(value){
              this.timeDown();
            }
          }
      },time : {
        handler(value){
         
          if(value==0){
            eventBus.$emit('show',true);
            this.finish=true;
            this.content = false;
          }
        }
      },playAgain : function() {
        if(this.playAgain){
         
          this.text = [];
          this.content=true,
          this.start = false,
          this.finish = false,
          this.time = 60;
          eventBus.$emit('playAgain',false);
          this.$refs.typeBox.innerHTML = null;
          this.$refs.typeBox.focus();
          this.SetData();
          this.point = {correct : [], wrong: []};
        }
      }
    },
    computed: {

      textLastArr : function(){
        let last = this.textValue.length;
        if(last>0){
            return this.textValue[last-1];
        }else {
          return this.datas[0];
        }

      },contentCom : function(){
        if(this.content){
          return true;
        }else {
          return false;
        }
      }
    },
    directives: {
    focus: {
      // directive definition
      inserted: function (el) {
        el.focus();
      }
    
    }
    },
    components: {
      KeyboardVue
    },
    mounted(){
      this.SetData();
      eventBus.$on('count',(value)=> {
          this.count = value;
      });

      eventBus.$on('playAgain',(value)=> {
          this.playAgain = value;
      });

     
    }
  }
</script>


<style scoped>
[contenteditable] {
  outline: 0px solid transparent;
}
</style>