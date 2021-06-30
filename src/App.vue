<template >
  
  <div id='main' :class="isDay ? 'day' : 'night' ">
    <!--Header starts -->
    <div style="text-align:center; margin-top:50px">
      <h1>Weather app</h1>
      <p>2021 © Yassine Kharrat</p>
    </div>
    <!--Header finsihes -->
    <div class="container my-5">
      <form class="search-location" v-on:submit.prevent = 'getCity'>
        <input
          id="city_name"
          type="text"
          :text="this.city_search"
          class="  form-control form-rounded p-2"
          placeholder="What City?"
          autocomplete="off"
        />
      </form>
      <transition name="fade" >
        <div
          :class="['card rounded', 'my-3', 'shadow-lg','overflow-hidden', isDay ? 'back-card' : 'back-card-night']"
          v-if="visible"
        >
          <!-- Top of card starts here -->
          <div class="card-top text-center" style="margin-bottom: 230px">
            <div class="city-name my-3">
              <p>{{this.country}}</p>
              <span>...</span>
              <p class="">{{this.city}}</p>
            </div>
          </div>
          <!-- top of card ends here -->

          <!--card middle body, card body used cos I want to just update the innerHTML -->
          <div class="card-body">
            <!-- card middle starts here -->
            <div class="card-mid">
              <div class="row">
                <div class="col-12 text-center temp">
                  <span>{{this.temperature}}&deg;C</span>
                  <p class="my-4">{{this.desc}}</p>
                </div>
              </div>
              <div class="row">
                <div class="col d-flex justify-content-between px-5 mx-5">
                  <div>
                    <p class="Min">Min:</p>
                    <p>{{this.min_temp}}&deg;C</p>
                  </div>
                  <div>
                    <p class="Max">Max:</p>
                    <p>{{this.max_temp}}&deg;C</p>
                  </div>
                </div>
              </div>
            </div>
            <!-- card middle ends here -->

            <!-- card bottom starts here -->
            <div class="card-bottom px-5 py-4 row">
              <div class="col text-center">
                <p>{{this.feels_like}}&deg;C</p>
                <span>Feels like</span>
              </div>
              <div class="col text-center">
                <p>{{this.humidity}}%</p>
                <span>humidity</span>
              </div>
            </div>

            <!-- card bottom ends here -->
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
    return{
      visible: false,
      isDay: true,
      city_search: "",
      APIkey: 'c2acfc81a7eb47f11848d82b3d106e42',
      country: 'Tunsia',
      city:"Kairouan",
      temperature: 35,
      desc: 'Cloudy',
      min_temp: 20,
      max_temp: 30,
      feels_like: 25,
      humidity: 55

    }
  },
  methods: {
    async getCity(){
      if (document.getElementById('city_name').value.length != 0){
        try {
          console.log("If you see this then you're a coder! :)")
          const baseURL = `https://api.openweathermap.org/data/2.5/weather?q=${document.getElementById('city_name').value}&appid=${this.APIkey}`;
          const res = await fetch(baseURL);
          const data = await res.json();
          console.log(data);
          this.city_search = document.getElementById('city_name').value;
          this.city  = data.name;
          this.country = data.sys.country;
          this.temperature = Math.round(parseFloat(data.main.temp - 273.15).toFixed(1));
          this.min_temp = Math.round(parseFloat(data.main.temp_min - 273.15).toFixed(1));
          this.max_temp =  Math.round(parseFloat(data.main.temp_max - 273.15).toFixed(1));
          this.feels_like =  Math.round(parseFloat(data.main.feels_like - 273.15).toFixed(1));
          this.humidity = data.main.humidity;
          this.desc = data.weather[0].description;
          //Check day or night by the last character of icon 'n' or 'd'
          const timeOfDay = data.weather[0].icon;
          if(timeOfDay.includes('n')){
            this.isDay = false;
          }
          else{
            this.isDay = true;
          }
          this.visible = true;
          console.log(this.isDay);
        }
        catch {
          alert("An error occured!")
          this.visible = false;
        }      
      }
        
      else{
        this.visible = false;
      }
    }
  }
}
</script>

<style lang="scss">
@import './assets/card.css';


*{
  font-family: 'Itim', cursive;
}
.title{
  text-align: center;
  margin-top: 10px;
}

.Min{
  font-size: 15px;

}

.Max{
  font-size: 15px;
}

.fade-enter-from{
  opacity: 0;
}
.fade-enter-to{
  opacity: 1;
}
.fade-enter-active{
  transition: all 0.6s ease-in;
}
.fade-leave-from{
  opacity: 1;
}
.fade-leave-to{
  opacity: 0;
}
.fade-leave-active{
  transition: all 1.5s ease-out;
}

</style>
