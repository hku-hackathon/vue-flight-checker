<template>
  <div>
    <v-container style="background-color: #D8E1F6">
      <v-row style="background-color:#7D9AE3">
        <v-col>
          <h1 class="text-center">Hong Kong Airport</h1>
        </v-col>
      </v-row>
      <v-row class="pt-4">
        <v-col cols="3"></v-col>
        <v-col cols="6">
          <v-row align="center">
            <v-text-field
              solo
              clearable
              label="Search"
              hide-details
              prepend-inner-icon="mdi-magnify"
            ></v-text-field>
            <div class="mx-1"></div>
            <v-menu
              v-model="menu"
              :close-on-content-click="false"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="primary"
                  large
                  elevation="2"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon>mdi-calendar</v-icon>
                </v-btn>
              </template>
              <v-date-picker
                v-model="pickedDate"
                no-title
                scrollable
              >
                <v-spacer></v-spacer>
                <v-btn
                  text
                  color="primary"
                  @click="menu = false"
                >
                  Cancel
                </v-btn>
                <v-btn
                  text
                  color="primary"
                  @click="menu = false; fetchApi()"
                >
                  OK
                </v-btn>
              </v-date-picker>
            </v-menu>
          </v-row>
        </v-col>
        <v-col cols="3"></v-col>

      </v-row>
      <div
        class="mx-5 my-3"
        :key="JSON.stringify(flight)"
        v-for="flight in flightList"
      >
        {{ flight.time }}
        <v-row
          no-gutters
          class="my-3"
          :key="JSON.stringify(f)"
          v-for="f in flight.flight"
        >
          <v-col>
            <v-card class="rounded-lg">
              <v-row no-gutters>
                <v-col cols="2">
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <!-- Airline Logo -->
                    <v-img :src="`/airline_logos/${f.no.split(' ')[0]}.png`">
                    </v-img>
                  </v-row>
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <v-card-title>
                      {{f.no}}
                    </v-card-title>
                  </v-row>
                </v-col>
                <v-col cols="5">
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <h2>Destination:</h2>
                  </v-row>
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <h3>{{ flight.destination[0] }}</h3>
                  </v-row>
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <h2>----</h2>
                  </v-row>
                </v-col>
                <v-col cols="5">
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <h2>Departure:</h2>
                  </v-row>
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <h3>{{ date }}</h3>
                  </v-row>
                  <v-row
                    no-gutters
                    justify="center"
                  >
                    <h3>{{ flight.status }}</h3>
                  </v-row>
                </v-col>
              </v-row>
            </v-card>
          </v-col>
        </v-row>
      </div>
    </v-container>

  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "FlightList",
  props: {
    flights: Array,
  },
  data() {
    return {
      data: [],
      date: "",
      menu: false,
      arrival: false,
      cargo: false,
      flightList: [],
      pickedDate: new Date().toISOString().slice(0, 10),
    };
  },
  methods: {
    fetchApi() {
      axios
        .get(
          `http://localhost:8080/flightinfo-rest/rest/flights/past?date=${this.pickedDate}&arrival=false&cargo=false&lang=en`
        )
        .then((response) => (this.data = response.data));
    },
  },
  mounted() {
    this.fetchApi();
  },
  watch: {
    data(d) {
      d = d[0];
      console.log("Got new data.");
      console.log(d);
      this.date = d.date;
      this.arrival = d.arrival;
      this.cargo = d.cargo;
      this.flightList = d.list;
    },
  },
};
</script>

<style lang="css" scoped>
.v-text-field >>> input {
  font-size: 1.4em;
  padding: 30px 0 30px;
}
.v-text-field >>> label {
  font-size: 1.4em;
}
.v-text-field >>> button {
  font-size: 1.4em;
}
</style>


