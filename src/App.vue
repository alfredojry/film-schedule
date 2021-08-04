<template>
  <ScheduleTable :filas="filas" />
</template>

<script>
import ScheduleTable from './components/ScheduleTable.vue'
import txt from 'raw-loader!./assets/GRADE_DESTAQUE_688.txt';
const lines = txt.split(/\n/g)
  .filter(Boolean) // descartando linhas vazias
  .filter(s => !/^\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2}/.test(s)); // descartando datas
const strDataemTxt = txt.match(/\d\d:\d\d:\d\d/g)[0]; // pega a data do arquivo GRADE_DESTAQUE_688.txt
const [horasTxt, minutosTxt, segundosTxt] = strDataemTxt.split(':').map(Number);
const dataEmTxt = new Date();
dataEmTxt.setHours(horasTxt);
dataEmTxt.setMinutes(minutosTxt);
dataEmTxt.setSeconds(segundosTxt);
console.log(dataEmTxt)

export default {
  name: 'App',
  components: {
    ScheduleTable,
  },
  data() {
    return {
      filmes: [],
      filas: [],
      timestamp: dataEmTxt.getTime(),
      simulacaoMinuto: 1000,
    }
  },
  mounted() {
    this.carregarFilmes();
    this.agruparFilmes();
    this.ordenarSessoes();
    setInterval(() => {
      this.limparEstado();
      this.timestamp = this.timestamp + 60000;
      console.log(new Date(this.timestamp))
      this.carregarFilmes();
      this.agruparFilmes();
      this.ordenarSessoes();
    }, this.simulacaoMinuto)
  },
  created() {},
  beforeUnmount() {},
  methods: {
    carregarFilmes() {
      for (const line of lines) {
        const filme = {
          sala: line.slice(0, 2),
          codigoEvento: line.slice(2, 10),
          horaSessao: line.slice(10, 15),
          nomeFilme: line.slice(15, 75).trim(),
          idadeMin: line.slice(75, 77),
          vazio: line[78],
          siglaLegenda: line[80],
          sessao: line[81],
          preEstreia: line[82],
          _3D: line[83],
          vip: line[84],
          duracao: line.slice(85, 88),
          lotacao: line[89],
          exibicao: line.slice(91, 93),
          distribuidora: line.slice(94, 134).trim(),
          genero: line.slice(134, 174).trim(),
          poltrona: line.slice(174, 215).trim(),
          codigoAncine: line.slice(215, 229),
          destaque: line[230],
        };
        filme.poltronaEspecial = filme.poltrona.includes('VP') ? 'VP' 
          : filme.poltrona.includes('DB') ? 'DB' : '';
        this.filmes = [...this.filmes, filme];
      }
    },
    agruparFilmes() {
      for (const filme of this.filmes) {
        // obter os minutos antes da sessao
        const dataSessao = new Date();
        const [horas, minutos] = filme.horaSessao.split('h').map(Number);
        dataSessao.setHours(horas);
        dataSessao.setMinutes(minutos);
        dataSessao.setUTCSeconds(0);
        const minutosAntesSessao = ( dataSessao.getTime() - this.timestamp ) / (1000 * 60);
        //
        const legenda = filme.siglaLegenda === 'L'
          ? 'LEG'
          : filme.siglaLegenda === 'D'
            ? 'DUB'
            : filme.siglaLegenda === 'V'
              ? 'ORI'
              : '';
        //
        if (minutosAntesSessao > 0) {
          const index = this.filas.findIndex(item => 
          item.nomeFilme === filme.nomeFilme
            && item.exibicao === filme.exibicao
            && item.poltronaEspecial === filme.poltronaEspecial 
          );
          if (index > -1) {
            const row = { ...this.filas[index] };
            row.sessoes.push({ 
              horaSessao: filme.horaSessao.replace('h', ':'),
              sessaoVerde : minutosAntesSessao < 15,
              minutosAntesSessao,
              sessaoEsgotada: filme.sessao === 'S',
              legenda,
            });
            this.filas = [
              ...this.filas.slice(0, index),
              row,
              ...this.filas.slice(index + 1)
            ];
          } else {
            this.filas = [...this.filas, {...filme, sessoes: [{
              horaSessao: filme.horaSessao.replace('h', ':'),
              sessaoVerde: minutosAntesSessao < 15,
              minutosAntesSessao,
              sessaoEsgotada: filme.sessao === 'S',
              legenda,
            }]}];
          }
        }
      }
    },
    ordenarSessoes() {
       const faixas = {
         '0': '#00af51', // reservado nao pode ser usado
         '10': '#00ccff',
         '12': '#ffcc00',
         '14': '#ff6600',
         '16': '#fe0000',
         '18': '#000000',
    };
      for (const fila of this.filas) {
        fila.sessoes.sort((a, b) => a.minutosAntesSessao - b.minutosAntesSessao);
        fila.corIdade = !Number(fila.idadeMin) ? '#00af51' : faixas[fila.idadeMin];
      }
    },
    limparEstado() {
      this.filmes = [];
      this.filas = [];
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;*/
  color: #2c3e50;
  margin-top: 60px;
}
</style>
