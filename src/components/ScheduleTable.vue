<template>
    <div id="schedule-table">
        <div class="table-filmes">
            <div class="row-filme" v-for="fila in filas" :key="fila">
                <div class="celda-nome-filme">{{ fila.nomeFilme }}</div>
                <div class="celda-idade-min">
                    <span v-bind:style="{ backgroundColor: fila.corIdade }">
                        {{ Number(fila.idadeMin) ? fila.idadeMin : 'L' }}
                    </span>
                </div>
                <div class="celda-exibicao-poltrona">{{ fila.exibicao === 'NO' ? '2D' : fila.exibicao}}{{ fila.poltronaEspecial }}</div>
                <div class="celda-sessoes">
                    <div class="celda-sessao" v-for="sessao in fila.sessoes" :key="sessao">
                        <div class="hora-sessao">{{ sessao.horaSessao }}</div>
                        <div v-if="sessao.sessaoEsgotada" class="esgotada">ESGOTADA</div>
                        <div v-else class="legenda">{{ sessao.legenda }}</div>
                        <div v-if="sessao.sessaoEsgotada && fila.poltronaEspecial === 'XD' " class="dbox">
                            DBOX
                        </div>
                    </div>
                    <div 
                        class="celda-sessao" 
                        v-for="sessao in Array.from({length: 6 - fila.sessoes.length})" 
                        :key="sessao">
                    </div>
                    <div 
                        class="celda-sessao" 
                        v-for="sessao in Array.from({
                            length: (fila.sessoes.length > 6 && 12 - fila.sessoes.length)
                        })" :key="sessao">
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'schedule-table',
    props: {
        filas: Array,
    },
}
</script>

<style scoped>

.table-filmes {
    font-weight: bolder;
}

.row-filme {
    display: flex;
    width: 100%;
    align-items: stretch;
    outline: 1px solid #8b8d8f;
}

.celda-nome-filme {
    width: 25%;
    background-color: #212325;
    color: #fff;
    display: flex;
    align-items: center;
    outline: 1px solid #8b8d8f;
    padding-left: 5px;
    box-sizing: content-box;
}

.celda-idade-min {
    width: 5%;
    text-align: center;
    background-color: #2c2e30;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    outline: 1px solid #8b8d8f;
}

.celda-idade-min > span {
    padding: 6px;
    border-radius: 2px;
    width: 20px;
    box-sizing: content-box;
    box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.5);
}

.celda-exibicao-poltrona {
    width: 10%;
    text-align: center;
    background-color: #62615c;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    outline: 1px solid #8b8d8f;
    font-size: 28px;
}

.celda-sessoes {
    width: 60%  ;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    background-color: #acaeb0;
}

.celda-sessao {
    width: 16.66%;
    height: 10vh;
    text-align: center;
    outline: 1px solid #8b8d8f;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
}

.hora-sessao {
    width: 100%;
    font-size: 24px;
}

.legenda {
    font-size: 12px;
    border-radius: 8px;
    padding-left: 4px;
    padding-right: 4px;
    box-shadow: 1px 1px 1px 1px rgba(0, 0, 0, 0.5);
    margin: 4px;
}

.esgotada {
    font-size: 12px;
    margin: 4px;
}

.dbox {
    font-size: 12px;
    margin: 4px;
    color: yellow;
}

</style>