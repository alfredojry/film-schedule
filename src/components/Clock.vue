<template>
    <div id="clock">
        <div>{{ `${strHoras}:${strMinutos}` }}</div>
    </div>
</template>

<script>
export default {
    name: 'clock',
    props: {
        timestamp: Number,
    },
    data() {
        return {
            strHoras: '',
            strMinutos: '',
          };
    },
    watch: {
        timestamp() {
        const objData = new Date(this.timestamp);
        this.strHoras = objData.getHours() < 10 ? `0${objData.getHours()}` : objData.getHours();
        this.strMinutos = objData.getMinutes() < 10 ? `0${objData.getMinutes()}` : objData.getMinutes()
        }
    },
}
</script>

<style scoped>

    #clock {
        display: flex;
        justify-content: center;
        margin-top: -30px;
        margin-bottom: 20px;
        font-size: 28px;
        font-weight: bolder;
    }

    #clock > div {
        width: 100px;
        padding: 5px;
        text-align: center;
        box-shadow: 1px 1px 1px 1px rgba(0, 0, 0, 0.5);
        background-color: #212325;
        color: white;
    }

</style>