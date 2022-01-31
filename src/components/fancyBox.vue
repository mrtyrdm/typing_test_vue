<template>
    <div v-show="show" class="absolute w-full h-full flex items-center justify-center bg-black/75  z-10">
      <div  class="bg-white w-1/2 text-center	p-12 rounded-lg drop-shadow-2xl opacity-100 flex flex-wrap items-center">
        <div class="w-full flex justify-center mb-6">
            <img  class="w-2/4 rounded-lg" :src="gifSrc" alt="">
        </div>
        <div class="w-full text-5xl font-black mb-4">Süreniz Bitti</div>
        <div class="w-full flex flex-wrap my-4 justify-center">
            <div class="flex flex-col  text-xl font-black text-green-500">
                <span class="p-4 border w-20 h-20 flex items-center justify-center border-8 border-green-500 rounded-full text-3xl">{{point.correct.length}}</span>
                <span>Doğru</span>
            </div>
            <div class="items-center flex text-4xl font-black mx-4 mb-8">-</div>
            <div class="flex flex-col  text-xl font-black text-red-500">
                <span class="p-4 border w-20 h-20 flex items-center justify-center border-8 border-red-500 rounded-full text-3xl">{{point.wrong.length}}</span>
                <span>Yanlış</span>
            </div>
            <div class="items-center flex text-4xl font-black mx-4 mb-8">=</div>
            <div class="flex flex-col  text-xl font-black text-orange-500">
                <span class="p-4 border w-20 h-20 flex items-center justify-center border-8 border-orange-500 rounded-full text-3xl">{{point.correct.length - point.wrong.length}}</span>
                <span>Puan</span>
            </div>

        </div>
        
        <div class="w-full mt-6">
            <button @click="playAgain" class="bg-red-600 hover:bg-black px-6 py-2 duration-300 hover:shadow-xl rounded-lg text-2xl text-white uppercase font-black">Tekrar Oyna</button>
        </div>
      </div>
    </div>
</template>

<script>
import eventBus from "./eventBus"

export default {
    data(){
        return {
            point : {correct : [], wrong: []},
            time: null,
            show : false,
        }
    },
    methods: {
        playAgain : function(){
            this.show = false;
            eventBus.$emit('playAgain',true);
        }
    },
    computed: {
        gifSrc : function(){
            var score =  this.point.correct.length-this.point.wrong.length
            if(score<20){
                return require('@/assets/val1.gif');
            }else if(score>30){
                return require('@/assets/val2.gif');
            }else {
                return require('@/assets/val3.gif');
            }
        }
    },
    watch: {
        time : {
            handler(value){
                if(value==0){
                    this.show = true;
                }
            }
        }
    },
    created(){
        eventBus.$on('point',(point)=> {
            this.point = point;
        });

        eventBus.$on('show',(time)=> {
            this.show = time;
        })
    }

}
</script>