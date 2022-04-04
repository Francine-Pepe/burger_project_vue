<template>
    <div id="main_banner">
        <h1>Make your Burger</h1>
    </div>
</template>

<script>
export default {
    name: "Banner"
}
</script>

<style scoped>
    #main_banner {
        background-image: url('/public/img/burger.jpg');
        background-position: 0;
        background-size: cover;
        height: 50vh;
        display: flex;
        align-items: center;
        justify-content: flex-start;
    }

    #main_banner h1 {
        color: #fff;
        text-align: center;
        font-size: 4vw;
        background-color: #222;
        padding: 20px 40px;
    }

</style>