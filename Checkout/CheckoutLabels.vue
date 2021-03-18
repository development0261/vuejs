<template>
    <div class="labels">
        <div class="labels-container" :class="{'labels-container--advanced': step}">
            <div class="labels-container-step" v-for="(label,index) in labels"
                 :key="`label_${index}`" v-show="index === step" v-text="label"/>
        </div>
    </div>
</template>

<script>
    export default {
        name: "CheckoutLabels",
        props: {
            labels: {
                type: Array,
                required: true
            },
            step: {
                type: Number,
                required: true
            }
        },

    }
</script>

<style lang="scss" scoped>
    .labels {
        border: 1px solid rgba(0, 0, 0, .8);
        background-color: rgba(0, 0, 0, .8);
        border-radius: 9px;
        padding: 9px;

        &--small {
            padding: 6px;

            .labels-container-step {
                padding: 5px 0;
                font-size: 15px;
            }

        }

        &-container {
            width: 70%;
            transition: width 1s;
            background-color: #f60;
            border-radius: 6px;

            &--advanced {
                width: 80%;
            }

            &-step {
                color: #fff;
                padding: 10px 0;
                width: 100%;
                font-size: 18px;
                text-align: center;
                font-weight: 700;
            }
        }
    }
</style>
