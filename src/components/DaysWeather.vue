<template>
    <div class="days-tab text-center">
        <div v-if="loading" class="loading">Loading ....</div>
        <ul v-else class="p-0">
            <li v-for="day in forecast" :key="day.date" class="li-active">
                <div class="py-3"><img :src="iconUrl"></div>
                <div class="py-3">{{day.date}}</div>
                <div class="py-3">{{day.temperature}}:&deg;C</div>
            </li>
        </ul>
    </div>
</template>
  
  
  
  
  
  
  
  
  
  
  
<script>

import axios from 'axios';
import moment from 'moment';

export default {
    props: {
        cityname: String,
    },
    data(){
        return {
            forecast: [],
            loading: null,
            iconUrl: null,
        };
    },

    mounted(){
        this.fetchWeatherData();
    },
    methods: {
        async fetchWeatherData(){
            const apiKey = '239c6eca0d4cd9e1d9ec4e06de18eed6';
            const city = this.cityname;
            const apiUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`

            await axios.get(apiUrl).then(Response =>{
                const forecastData = Response.data.list;
                const filterData = forecastData.map(item => {
                    return {
                        date: moment(item.dt_txt.split(' ')[0]),
                        temperature: Math.round(item.main.temp),
                        description : item.weather[0].description,
                        iconUrl: `https://api.openweathermap.org/img/w/${item.weather[0].icon}.png`,
                    };
                }).reduce((acc, item) => {
                    if(!acc.some(day => day.date.isSame(item.data, 'day'))){
                        acc.push(item);
                    }
                    return acc;
                }, []).slice(1, 5);
                console.log(filterData, "working")
            }).catch(error => {
                console.error( 'Error fetching weather data: ', error);
                this.loading = false;
            })
        }
    }
}

</script>
  
  
  
  
<style>
.days-tab {
    width: 90%;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.2);
    border-radius: 20px;
    width: 90%;
    margin: auto;
    background-color: #28303b;
}

.loading {
    color: #fff;
}

ul {
    margin: 0;
}

li {
    display: inline-block;
    list-style: none;
    height: 100%;
    width: 21%;
    max-width: 21%;
    font-size: 1vw;
    line-height: 1.2;
}

span {
    display: block;
    margin-bottom: 5px;
    font: 100% sans-serif;
    height: 35px;
}

.li-active {
    background: #253d5c;
    color: #222831;
    border-radius: 10px;
    margin: 0.5rem;
    color: #fff;
    font-weight: 600;
}

.li-active:hover {
    transform: scale(1.2);
    transition: transform 0.1s ease;
}

.li-active-temp {
    display: inline-block;
    background-color: #222831;
    color: #ffffff;
    transition: background-color 0.5s;
    border-radius: 10px;
}

.li-active-temp:hover {
    transform: scale(1.2);
    transition: transform 0.1s;
    background: #fff;
    border-radius: 10px;
    color: #191a1f;
}
</style>
  