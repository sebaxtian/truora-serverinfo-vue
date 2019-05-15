<template>
  <b-container>
    <!-- <b-row>
      <b-col>ServerInfo Component</b-col>
    </b-row>-->
    <b-row>
      <b-col>
        <b-form>
          <b-form-group
            v-bind:disabled="isSearching()"
            label
            label-for
            description="Dominio para obtener información"
          >
            <b-form-input
              v-model="domainQuery"
              :state="validation"
              required
              placeholder="truora.com"
              disabled-field
            ></b-form-input>
            <b-form-invalid-feedback :state="validation">Debe ingresar un dominio válido.</b-form-invalid-feedback>
          </b-form-group>
          <b-form-group id>
            <b-button
              v-if="searching === false"
              block
              variant="primary"
              v-on:click="search"
            >Consultar</b-button>
            <b-button v-if="searching === true" block variant="primary" disabled>
              <b-spinner small type="border"></b-spinner> Consultando ...
            </b-button>
          </b-form-group>
        </b-form>
      </b-col>
    </b-row>
    <b-row>
      <b-col>
        <b-alert
          :show="dismissCountDown"
          dismissible
          variant="danger"
          @dismissed="dismissCountDown=0"
          @dismiss-count-down="countDownChanged"
        >No fue posible consultar información del dominio {{ domainQuery }}</b-alert>
      </b-col>
    </b-row>
    <b-row>
      <b-col>
        <b-card v-if="isFind === true" :title="domainQuery" :sub-title="sslGrade">
          <b-card-text>
            <p>
              <strong>Last updated:</strong>
              {{ lastUpdated }}
            </p>
            <p>
              <strong>Is down:</strong>
              {{ isDown }}
            </p>
            <p>
              <strong>Servers changed:</strong>
              {{ serversChanged }}
            </p>
            <p>
              <strong>Previous ssl grade:</strong>
              {{ previousSslGrade }}
            </p>
            <p>
              <strong>Logo:</strong>
              <b-link :href="logo" target="_blank">{{ logo }}</b-link>
            </p>
            <!-- <b-img v-bind="logoProps" :src="logo" alt="Domain logo" center></b-img> -->
          </b-card-text>

          <h5>Servidores</h5>
          <b-table responsive striped outlined hover :items="listServers"></b-table>
        </b-card>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
const axios = require("axios");

const URL_API_REST = "http://localhost:3333";
const ENDPOINT_INFOSERVER = URL_API_REST + "/infoserver?domain=";

export default {
  name: "ServerInfo",
  data: function() {
    return {
      domainQuery: "",
      dismissSecs: 5,
      dismissCountDown: 0,
      searching: false,
      isFind: false,
      listServers: [],
      sslGrade: "",
      lastUpdated: "",
      isDown: false,
      serversChanged: false,
      previousSslGrade: "",
      logo: "",
      logoProps: { width: 32 }
    };
  },
  computed: {
    validation() {
      // console.log(/(?:[a-z0-9](?:[a-z0-9-]{0,61}[a-z0-9])?\.)+[a-z0-9][a-z0-9-]{0,61}[a-z0-9]/.test(this.domainQuery));
      this.domainQuery = this.domainQuery.replace(/ /g, "");
      this.domainQuery = this.domainQuery.trim();
      this.isFind = false;
      return /(?:[a-z0-9](?:[a-z0-9-]{0,61}[a-z0-9])?\.)+[a-z0-9][a-z0-9-]{0,61}[a-z0-9]/.test(
        this.domainQuery
      );
    }
  },
  methods: {
    countDownChanged(dismissCountDown) {
      this.dismissCountDown = dismissCountDown;
    },
    showAlert() {
      this.dismissCountDown = this.dismissSecs;
    },
    setSearching(isSearch) {
      this.searching = isSearch;
    },
    isSearching() {
      return this.searching;
    },
    setIsFind(isFind) {
      this.isFind = isFind;
    },
    search: function() {
      console.log("Inicia consulta de información");
      const setSearching = this.setSearching;
      setSearching(true);
      const showAlert = this.showAlert;
      const setIsFind = this.setIsFind;
      setIsFind(false);
      const showInfoServer = this.showInfoServer;
      // Make a request for a domain servers information
      axios
        .get(ENDPOINT_INFOSERVER + this.domainQuery)
        .then(function(response) {
          // handle success
          // console.log(response);
          if (response.status == "200") {
            const dataInfoServe = response.data;
            console.log("dataInfoServe: ", dataInfoServe);
            showInfoServer(dataInfoServe);
            setIsFind(true);
          } else {
            showAlert();
          }
        })
        .catch(function(error) {
          // handle error
          console.log(error);
          showAlert();
        })
        .finally(function() {
          // always executed
          setSearching(false);
        });
    },
    showInfoServer: function(dataInfoServe) {
      this.listServers = dataInfoServe.Servers;
      this.sslGrade = "SSL " + dataInfoServe.SslGrade;
      this.lastUpdated = dataInfoServe.LastUpdated;
      this.isDown = dataInfoServe.IsDown;
      this.serversChanged = dataInfoServe.ServersChanged;
      this.previousSslGrade = dataInfoServe.PreviousSslGrade;
      this.logo = dataInfoServe.Logo;
    }
  }
};
</script>

<style>
</style>
