<template>
    <div class="installments">
        <label class="installments__label">
            <input type="radio" class="installments__input" name="installments" :value="0" v-model="installments">
            <span class="installments__text">Pay in full</span>
            <span v-if="! installments" class="installments__price" v-text="`${currency.currency} ${currency.symbol}${price}`"/>
        </label>
        <label class="installments__label">
            <input type="radio" class="installments__input" name="installments" :value="1" v-model="installments">
            <span class="installments__text">Pay in installments</span>
        </label>
        <template v-if="installments">
            <div class="installments__wrapper">
                <div class="installments__description" v-html="options.description"/>
                <div class="installments__summary" v-html="installmentPricingText"/>
            </div>
        </template>
    </div>
</template>

<script>
    export default {
        name: "Installments",

        props: {
            options: {
                type: Object,
                required: true
            },
            price: {
                type: String,
                required: true
            },
            currency: {
                type: String,
                required: true
            }
        },

        data() {
            return {
                installments: 0,
                installmentPrice: 0,
                parcels: [
                    "First", "Second", "Third", "Fourth", "Fifth", "Sixth", "Seventh", "Eighth", "Ninth", "Tenth"
                ]
            };
        },

        mounted() {
            let price = this.options.price;
            if (this.options.discount) {
                price = this.options.discountPrice;
            }
            this.installmentPrice = (parseFloat(price) / parseInt(this.options.number)).toFixed(2);
        },

        watch: {
            installments(value) {
                this.$emit('installments', value)
            }
        },

        computed: {
            installmentPricingText() {
                const firstPrice = (parseFloat(this.installmentPrice) + parseFloat(this.options.fee)).toFixed(2);
                let text = this.options.today.replace('[price]', `<span class="installments__price">${this.currency.currency} ${this.currency.symbol}${firstPrice}</span>`);
                const date = new Date();
                for (let i = 1; i < this.options.number; i++) {
                    date.setDate(date.getDate() + parseInt(this.options.interval));
                    text += '<br>';
                    text += this.options.others.replace('[price]', `<span class="installments__price">${this.currency.currency} ${this.currency.symbol}${this.installmentPrice}</span>`)
                        .replace('[date]', date.toLocaleDateString()).replace('[installment]', this.parcels[i]);
                }
                return text;
            }
        }
    }
</script>

<style lang="scss">
    @import "~bootstrap/scss/functions";
    @import "~bootstrap/scss/variables";
    @import "~bootstrap/scss/mixins";

    .installments {
        text-align: left;
        width: 98%;
        margin-left: auto;
        margin-right: auto;
        padding-bottom: 15px;
        color: #646464;

        @include media-breakpoint-up('lg') {
            width: 85%;
        }

        &__input {
            width: 22px;
            height: 22px;
            -webkit-appearance: none;
            -moz-appearance: none;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
            border-radius: 50%;
            background: #ffffff;
            margin-right: 5px;
            border: 1px solid rgb(230, 231, 234);
            outline: none;
            position: relative;
            vertical-align: middle;
            cursor: pointer;
            margin-left: 0;

            &:after {
                content: '';
                display: block;
                top: 5px;
                left: 5px;
                width: 10px;
                height: 10px;
                border-radius: 50%;
                background: #fff;
                position: absolute;
            }

            &:checked {
                border: 1px solid #b1b1b1;

                &:after {
                    background-color: #b1b1b1;
                }
            }
        }

        &__label {
            display: flex;
            align-items: center;
        }

        &__text {
            cursor: pointer;
        }

        &__price {
            font-weight: 700;
            font-size: 15px;
            margin-left: auto;

            @include media-breakpoint-up('lg') {
                font-size: 18px;
            }
        }

        &__wrapper {
            display: inline-block;
            text-align: right;
        }

        &__description {
            margin-left: 42px;
            margin-bottom: 12px;
        }

        &__summary {
            color: #646464;
            font-size: 13px;
            display: inline;

            @include media-breakpoint-up('lg') {
                font-size: 14px;
            }
        }
    }
</style>
