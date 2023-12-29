<template>

    <img
        class="max-h-40 m-auto"
        :src="pokemonImage"
    />

</template>


<script>


export default {
    data() {
        return {

        }
    },

    props: {
        pokemonImage: {
            type: String,
        }
    }

}

</script>
