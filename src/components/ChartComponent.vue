<template>
    <div v-if="!loading">
        <div class="container">
            <row class="title"><h2>Relatório Gosat</h2></row>
            <div class='report-display'>
                <canvas class='chart' v-if="dataPopulated" id="myChart"></canvas>
            </div>
        </div>
    </div>
</template>
  
  <script>
import Chart from "chart.js/auto";
import axios from "axios";

const headers = {
  "Content-Type": "application/json",
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
};

export default {
  name: "ChartComponent",

  data() {
    return {
      dataToBuild: null,
      loading: true,
      dataPopulated: false,
    };
  },

  methods: {
    async getReport() {
      await axios
        .get("http://localhost:8000/api/report", { headers })
        .then((dataToReport) => {
          this.dataToBuild = dataToReport.data;
          this.dataPopulated = true;
          this.loading = false;
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },

  mounted() {
    
    this.getReport().then(() => {
      const ctx = document.getElementById("myChart");
        this.dataToBuild = this.dataToBuild.sort(function(a,b){return a.valorAPagar - b.valorAPagar})
      new Chart(ctx, {
        type: 'bar',
    data: {
      labels: this.dataToBuild.map(x=>x.instituicaoFinanceira + ' (' + x.modalidadeCredito + ')'),
      datasets: [{
        label: '# Valor a Pagar',
        data: this.dataToBuild.map(x=>x.valorAPagar),
        borderWidth: 1
      }]
    },
    options: {
      scales: {
        y: {
          beginAtZero: true
        }
      }
    }
      });
    });
  },
};
</script>

<style>
    .report-display{
        width: 60%;
        margin: 10px auto; /* centraliza horizontalmente */
        display: block; /* opcional, caso o elemento não seja um bloco */
    }
    .title{
        text-align:center;
    }
    .chart{
        display: auto;
    }
</style>