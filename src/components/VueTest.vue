<template>
    <div id="app">
        <!--{{ auth_token }}-->
        <!--{{ country_list }}-->
        <center>
            <div class="half">
                <h1>Vue test application</h1>
                    <div class="form-group">
                        <span v-if="country_list.length" class="column-6 form-select">
                            <select v-model="country" placeholder="Select Country">
                                    <option value="" disabled="disabled" selected="selected">Choose a country</option>
                                    <option v-for="_country in country_list"  v-bind:key="_country.country_short_name" v-bind:value="_country.country_name">
                                        {{_country.country_name}}
                                    </option>
                            </select>
                        </span>
                        <span v-else>&#8212;&nbsp;<i>Loading countries</i>&nbsp;&#8212;</span>
                    </div>
                    <div class="form-group">
                        <span v-if="states_list.length" class="column-6 form-select">
                            <select v-model="state" placeholder="Select a State">
                                <option value="" disabled selected="selected">Choose a State</option>
                                <option v-for="_state in states_list"  v-bind:key="_state.state_name" v-bind:value="_state.state_name">
                                    {{_state.state_name}}
                                </option>
                            </select>
                        </span>
                        <!--<span v-else>&#8212;&nbsp;<i>Select a country first</i>&nbsp;&#8212;</span>-->
                    </div>
                    <div class="form-group">
                        <span v-if="cities_list.length">
                            <p>Now you can choose multiple cities</p>
                            <select multiple v-model="selected_cities" placeholder="Select a City">
                                <option v-for="_city in cities_list"  v-bind:key="_city.city_name" v-bind:value="_city.city_name">
                                    {{_city.city_name}}
                                </option>
                            </select>
                        </span>
                        <!--<span v-else>&#8212;&nbsp;<i>Select a state first</i>&nbsp;&#8212;</span>-->
                    </div>
                    <div class="answer">
                        <span v-if="selected_cities.length">You have selected {{selected_cities.length}} city(s).</span>
                        <span v-else>You did not select any city yet</span>
                    </div>
            </div>
        </center>
    </div>
</template>

<script>
    import axios from 'axios'
    //    import VueAxios from 'vue-axios'

    export default {
        name: 'VueTest',
        methods: {
            GetAccessToken(responseCallback) {
                axios.get("https://www.universal-tutorial.com/api/getaccesstoken", {

                    "headers": {
                        "Accept": "application/json",
                        "api-token": "5TJQSIPQ6BDzVIa9L_RHsjou30VuDnXFzfgmaOFASpmelRUKqjiSU0VeqWHi6frBtrk",
                        "user-email": "mohsin.mr@gmail.com"
                    }

                })
                    .then(response => responseCallback(response))
                    .catch(error => console.log(error))

            },
            GetCountryList(accessToken, responseCallback) {
                axios.get("https://www.universal-tutorial.com/api/countries/", {
                    "headers": {
                        "Authorization": "Bearer " + accessToken,
                        "Accept": "application/json"
                    }
                })
                    .then(response => responseCallback(response))
                    .catch(error => console.log(error))
            },
            GetStatesList(accessToken, countryName, responseCallback) {
                axios.get("https://www.universal-tutorial.com/api/states/"+countryName, {
                    "headers": {
                        "Authorization": "Bearer " + accessToken,
                        "Accept": "application/json"
                    }
                })
                    .then(response => {
                        responseCallback(response)
//                        console.log(response)
                    })
                    .catch(error => console.log(error))
            },
            GetCitiesList(accessToken, stateName, responseCallback) {
                axios.get("https://www.universal-tutorial.com/api/cities/"+stateName, {
                    "headers": {
                        "Authorization": "Bearer " + accessToken,
                        "Accept": "application/json"
                    }
                })
                    .then(response => {
                        responseCallback(response)
//                        console.log(response)
                    })
                    .catch(error => console.log(error))
            }
        },
        data() {
            return {
                auth_token: null,
                country_list: [],
                states_list: [],
                cities_list: [],
                country: "",
                state: "",
                city: "",
                selected_cities: []
            }
        },
        props: {
//            msg: String
        },

        watch: {
            country: function(val){
                this.GetStatesList(this.auth_token, val, (response) => {
                    this.states_list = response.data
                })
            },
            state: function(val){
                this.GetCitiesList(this.auth_token, val, (response) => {
                    this.cities_list = response.data
                })
            },
//            selected_cities: function(val){
//                console.log(val.length)
//            }

        },
        beforeMount() {
            this.GetAccessToken((response) => {
                console.log(response)
                this.auth_token = response?.data?.auth_token ?? false
                    this.GetCountryList(this.auth_token, (response) => {
                        this.country_list = response?.data ?? []
                    })

            })
        },
        mounted() {
        }
    }
</script>

<style scoped>
    h3 {
        margin: 40px 0 0;
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


    p {
        color: #6b6b6b;
        font-size: 12px;
    }



    select {
        position: relative;
        background: transparent;
        background-image: none;
        display: block;
        width: 100%;
        outline: none;
        color: #888888;
        cursor: pointer;
        z-index: 2;
        border: none;
        padding: 10px 10px 10px 5px;
        text-overflow: '';
        text-indent: 5px;

    @include appearance(none);

    &[multiple=multiple] {
         height: auto;
         border: 1px solid #cbcbcb;
         border-radius: 3px;
         box-shadow: 0 0 1px rgba(0, 0, 0, 0.1), inset 0 0 10px rgba(0, 0, 0, 0.07);

     @include single-transition(box-shadow, 0.2s);

    &:hover {
         border-color: #cccccc;
         box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
     }
    }

    option[disabled] {
        color: #eeeeee;
        text-shadow: none;
        border: none;
    }
    }

    select:-moz-focusring {
        color: transparent;
        text-shadow: 0 0 0 #888888;
    }

    select::-ms-expand {
        display: none;
    }



    /*==========  Generic styling  ==========*/

    .column-6 {
        /*position: absolute;*/
        /*top: 50%;*/
        /*left: 50%;*/
        margin: -20px 0 0 -150px;
        width: 300px;
    }
    .half{
        width: 400px;
    }
    .answer{
        margin-top:50px;
        color: #00548c;
        font-size: 20px;
    }
</style>
