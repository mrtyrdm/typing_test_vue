<template>
    <div class="bg-red-700 mx-64 my-8 rounded-lg p-4 drop-shadow-2xl">
        <div class="row " v-for="test in newkeys" :key="test.index">
        <div class="flex justify-center" v-for="test2 in test" :key="test2.index">
            <div v-for="test3 in test2" :key="test3.index">
                <button  class="p-4 bgcolor text-white m-2 w-16 rounded	h-16" :class="[(keyValue==test3)? 'test' : '']"  :value="keys">{{encode_utf8(test3)}}</button>
            </div>
        </div>
        </div>
    </div>
</template>

<script>

    import eventBus from "./eventBus";

    export default {
        data(){
            return {
                newkeys : [{rows: [81, 87, 69, 82, 84, 89, 85, 73, 79, 80, 219, 221]},{rows : [65, 83, 68, 70, 71, 72, 74, 75, 76, 186, 222]}, {rows: [90, 88, 67, 86, 66, 78, 77, 191, 220]}],
                keywords : [81, 87, 69, 82, 84, 89, 85, 73, 79, 80, 219, 221, 65, 83, 68, 70, 71, 72, 74, 75, 76, 186, 222, 90, 88, 67, 86, 66, 78, 77, 191, 220],
                keyValue : null,
                count: 0,
                text : []
            }
        },
        methods: {
            keys : function(){
                window.addEventListener('keydown',e=> {
                    let  key = e.which;
                    this.keyValue = key;
                    var bool = this.keywords.includes(key)
                    
                    if(bool){ 
                        this.count+=1;
                    }else if(key==8 && this.count>0) {
                        this.count-=1;
                    }
                    return key;
                })
            },   
            encode_utf8 : function(s) {
                const key = s;
                if(key==222){
                    return 'İ';
                }
                else if(key==219){
                    return 'Ğ';
                }
                else if(key==221){
                    return 'Ü';
                }
                else if(key==186){
                    return 'Ş';
                }
                else if(key==191){
                    return 'Ö';
                }
                else if(key==220){
                    return 'Ç';
                }
                return  String.fromCharCode(key);
            
            },         
        },
        computed : {
            getClassTest : function(event){
                if(event){
                    return 'line';
                }else {
                    return false;
                }
            },
        },
        watch : {
            text : function() {
                this.count = 0;
            },
            count : {
                handler(e){
                    eventBus.$emit('count',e);
                }
            }
        },
        mounted(){
            this.keys();
            eventBus.$on('addword',(text)=> {
                this.text = text;
            })
        }
    }
</script>


<style scoped>
.bgcolor {

    background: red;

    font-weight: bold;

    border-style: solid;
    border-top-width: 1px;
    border-bottom-width: 10px;
    border-left-width: 4px;
    border-right-width: 4px;
    border-color: rebeccapurple;
}

.test {
   animation-duration: 2s;
     animation-name: example;
}

@keyframes example {
  0% {border: 0;}
  100%   {    border-top-width: 1px;
    border-bottom-width: 10px;
    border-left-width: 4px;
    border-right-width: 4px;}

}
</style>