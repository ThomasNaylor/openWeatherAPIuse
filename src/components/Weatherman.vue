<template>
  <v-container fluid>
    <v-layout row wrap>
      <v-flex xs12 sm12 md6 lg6 pa-2>
        <h1>OpenWeatherApi Consumption</h1>
        <v-layout column justify-center>
          <v-expansion-panel  v-model="panel" popout expand>
            <v-expansion-panel-content
              v-for="item in items"
              :key="item.day"
              hide-actions>
              <v-layout slot="header" align-center row spacer>
                <v-flex xs4 sm2 md1>
                  <v-avatar
                    slot="activator"
                    size="46px">
                    <img
                      v-if="item.icon"
                      :src="iconImg + item.icon + '.png'"
                      alt="Avatar">
                  </v-avatar>
                </v-flex>

                <v-flex sm5 md3>
                  <p v-html="getDate(item.day)"></p>
                  </span>
                </v-flex>

                <v-flex no-wrap xs5 sm3>
                  <p class="weather__data" v-html="item.clouds"></p>
                </v-flex>

              </v-layout>

              <v-card>
                <v-divider></v-divider>
                <v-layout row>
                  <v-flex xs12>
                    <table class="weather__data">
                      <tr>
                        <td>Temperature:</td>

                        <td>{{ getTemp(item.temp) }}&#8451</td>
                      </tr>

                      <tr>
                        <td>Wind Speed:</td>
                        <td>{{ getSpeed(item.speed) }}km/p</td>
                      </tr>

                      <tr>
                        <td>Wind Direction:</td>
                        <td>{{ getDir(item.wind_deg) }}</td>
                      </tr>
                    </table>
                  </v-flex>
                </v-layout>
              </v-card>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-layout>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  name: 'weatherman',
  data: () => ({
      panel: [true, true],
      dialog: false,
      model: null,
      github: 'https://github.com/ThomasNaylor/openWeatherAPIuse',
      apikey: '181fd480069e2fb703efe45bb4204bfe',
      iconImg: 'https://openweathermap.org/img/w/',
      weatherdata: [],
      items: [],
    }),

    watch: {
      dialog (val) {
        val || this.close();
      }
    },

    created () {
      this.getGeolocation();
    },

    methods: {
      close () {
        this.dialog = false;
      },

      getGeolocation () {
        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(this.showPosition);
        } else {
          x.innerHTML = "Geolocation is not supported by this browser.";
        }
      },

      showPosition(position) {
        let coords = [];

        coords.push({
          latitude: position.coords.latitude,
          longitude: position.coords.longitude
        });

        this.getWeather(coords);
      },

      getWeather (coords) {
        var url = 'https://api.openweathermap.org/data/2.5/forecast?lat=' + coords[0].latitude + '&lon=' + coords[0].longitude + '&APPID=' + this.apikey;
        let data = [];

        this.axios.get(url)
        .then(response => {
          for(let i = 0; i < response.data.list.length; i+=8) {
            data.push({
              day: response.data.list[i].dt_txt,
              temp: response.data.list[i].main.temp,
              clouds: response.data.list[i].weather[0].description,
              wind_deg: response.data.list[i].wind.deg,
              speed: response.data.list[i].wind.speed,
              icon: response.data.list[i].weather[0].icon
            });
          }
          this.items = data;
        });
      },
      getDate(val) {
        return val.slice(0, 10);
      },
      getDir(val) {
        let dir = '';
        let temp = parseInt(val);

        switch(true) {
          case (val === 0):
            dir = 'Northly Wind';
            break;
          case (temp > 0 && temp < 90):
            dir = 'North Eastly Wind';
            break;
          case (temp === 90):
            dir = 'Eastly Wind';
            break;
          case (temp > 90 && temp < 180):
            dir = 'South Eastly Wind';
            break;
          case (temp === 180):
            dir = 'Southly Wind';
            break;
          case (temp > 180 && temp < 270):
            dir = 'South Westly Wind';
            break;
          case (temp === 270):
            dir = 'Westly Wind';
            break;
          case (temp > 270 && temp < 360):
            dir = 'North Westly Wind';
            break;
          default:
            dir = 'Undefined'
        }
        return dir;
      },
      getSpeed(val) {
        return Math.floor(val * 3.6);
      },
      getTemp(val) {
        return Math.floor(val - 273.15);
      }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.weather__data {
  text-transform: capitalize;
}
.weather__data tr td {
  padding: 10px;
}
.expansion__panel {
  background-color: #333333;
}
</style>
