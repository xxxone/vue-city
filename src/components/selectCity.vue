<template>
    <div class="wrap">
        <div class="searchBox">
            <input type="text" v-model="searchValue" placeholder="城市中文名或拼音">
            <span @click="hide">取消</span>
        </div>
        <div class="citys-searchlist" v-if="searchValue !== ''">
            <ul v-if="searchList.length!==0">
                <li class="bdb" v-for="item in searchList" @click="selectCity(item.name,item.id)">{{item.name}}</li>
            </ul>
            <div v-else class="nomatch">没有匹配城市</div>
        </div>
        <section class="view">
            <aside class="aside" v-if="topCity">
              <ul>
                <li v-for="(city,index) in topCity" @click='tapTopCity(city.id,index)'>
                  <a :class='highlight[index]' >{{city.name}}</a>
                </li>
              </ul>
            </aside>
            <div class="main" v-if="subCity">
              <ul>
                <li v-for="(city,index) in subCity">
                  <a @click="selectCity(city.name,city.id)"><span>{{city.name}}</span></a>
                </li>
              </ul>
            </div>
        </section>
    </div>
</template>

<script type="text/javascript">
    import {setStore,getStore} from "@/config/functions.js"
    //城市数据可以根据后台api提供,演示仅为测试数据
    const cityjson = '{'
            +   '"province" : ['
            +      '{"id" : "1", "name" : "北京市"},'
            +      '{"id" : "2", "name" : "山西省"},'
            +      '{"id" : "3", "name" : "河北省"}'
            +   '],'
            +   '"city" : ['
            +      '{"cid" : "1", "id" : "1", "name":"海淀区"},'
            +      '{"cid" : "1", "id" : "2", "name":"西城区"},'
            +      '{"cid" : "1", "id" : "2", "name":"东城区"},'
            +      '{"cid" : "1", "id" : "3", "name":"朝阳区"},'
            +      '{"cid" : "2", "id" : "4", "name":"太原市"},'
            +      '{"cid" : "2", "id" : "5", "name":"大同市"},'
            +      '{"cid" : "2", "id" : "6", "name":"阳泉市"},'
            +      '{"cid" : "3", "id" : "7", "name":"石家庄"},'
            +      '{"cid" : "3", "id" : "8", "name":"蚌埠市"},'
            +      '{"cid" : "3", "id" : "9", "name":"张家口"}'
            +   '],'
            +   '"county" : ['
            +      '{"cid" : "1", "id" : "1", "name":"中关村"},'
            +      '{"cid" : "1", "id" : "2", "name":"五道口"},'
            +      '{"cid" : "2", "id" : "3", "name":"西直门"},'
            +      '{"cid" : "2", "id" : "4", "name":"新街口"},'
            +      '{"cid" : "2", "id" : "5", "name":"小西天"},'
            +      '{"cid" : "3", "id" : "6", "name":"东直门"},'
            +      '{"cid" : "3", "id" : "7", "name":"雍和宫"},'
            +      '{"cid" : "3", "id" : "8", "name":"北新桥"},'
            +      '{"cid" : "5", "id" : "9", "name":"城区"},'
            +      '{"cid" : "5", "id" : "10", "name":"南郊区"},'
            +      '{"cid" : "5", "id" : "11", "name":"开发区"}'
            +   ']'
            +'}';
        const allCities = '{"A": [{"id": 36,"name": "阿拉善盟","pinyin": "alashanmeng"}]}'
    export default {
        data() {
            return {
                topCity: [],
                subCity: [],
                highlight: ['active', '', '', '', '', '', '', '', '', '', '', ''],
                searchValue: '',
                searchList: [], //搜索结果
            }
        },
        beforeCreate(){
            document.title = '城市选择';
        },
        mounted: function () {
            this.getTopCity();
            this.getSubCity(1)
        },
        watch: {
            searchValue: function() {
                this._search();
            }
        },
        methods: {
            selectCity:function(name,city_id){
                setStore("cityId",city_id);
                setStore("cityName",name);
                this.$router.go(-1);
            },
            tapTopCity: function (id,index) {
                this.setHighlight(index);
                this.getSubCity(id);
            },
            getTopCity: function () {
                this.topCity = JSON.parse(cityjson).province
            },
            getSubCity: function (id) {
                let city = JSON.parse(cityjson).city
                let newCity = []
                for (var i = 0; i < city.length; i++) {
                    if(city[i].cid == id){
                        newCity.push(city[i])
                    }
                }
                this.subCity = newCity
            },
            setHighlight: function (index) {
                var highlight = [];
                for (var i = 0; i < this.topCity; i++) {
                    highlight[i] = '';
                }
                highlight[index] = 'active';
                this.highlight = highlight;
            },
            _search: function() {
                var reg = new RegExp(this.searchValue == '' ? 'bj' :
                    this.searchValue, 'ig');

                var _arr = [];
                for(var i in JSON.parse(allCities)){
                    for(var j = 0; j <  JSON.parse(allCities)[i].length; j++){
                        if(
                            reg.test( JSON.parse(allCities)[i][j][
                                'name'
                            ]) ||
                            reg.test( JSON.parse(allCities)[i][j][
                                'pinyin'
                            ])
                        ){
                            _arr.push( JSON.parse(allCities)[i][j]);
                        }
                    }
                }
                this.searchList = _arr;
            },
            hide: function() {
                this.searchValue = '';
                this.searchList = [];
            },
        }
    }
</script>

<style lang="scss">
    @import '../assets/css/city.scss';
</style>
