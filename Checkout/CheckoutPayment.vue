<template>
    <div class="checkout-form__form-wrapper">
        <div class="payment-type" v-if="paypal">
            <label class="payment-type__label">
                <input type="radio" name="paymentType" checked value="stripe" v-model="paymentType"
                       class="payment-type__input">
                <LazyImg src="/images/visas.png"/>
            </label>
            <label class="payment-type__label">
                <input type="radio" name="paymentType" value="paypal" class="payment-type__input" v-model="paymentType">
                <LazyImg src="/images/paypal.png"/>
            </label>
        </div>
        <div v-show="paymentType === 'stripe'" class="stripe-container">
            <div class="stripe-container__form">
                <div id="card-element"/>
                <div id="cvc-element"/>
                <div id="expiry-element"/>
            </div>
            <secure-modal class="pricing__column-entry-secure">
                <LazyImg class="sprite-sheet-icon sprite-secure stripe-container__secure"
                     src="/images/spritesheet-icons.png"/>
             </secure-modal>
        </div>
        <div v-show="paymentType === 'paypal'">
            <LazyImg src="/images/paypalBig.png" class="payment-type__image"/>
        </div>
        <slot/>
    </div>
</template>

<script>
    import LazyImg from "../LazyImg";

    export default {
        name: "CheckoutPayment",
        components: {LazyImg},
        props: {
            paypal: {
                required: true,
                type: Boolean
            },
        },

        data() {
            return {
                paymentType: 'stripe',
                startYear: new Date().getFullYear(),
                focused: null,
                stripeStyle: {
                    style: {
                        base: {
                            fontFamily: '"Open Sans", arial, sans-serif',
                            fontSize: '13.5px',
                            fontWeight: 300,
                            color: '#646464'
                        }
                    }
                }
            }
        },

        mounted() {
            const elements = stripe.elements();
            const card = elements.create('cardNumber', this.stripeStyle);
            card.mount('#card-element');
            const cardExpiry = elements.create('cardExpiry', this.stripeStyle);
            cardExpiry.mount('#expiry-element');
            const cardCvc = elements.create('cardCvc', this.stripeStyle);
            cardCvc.mount('#cvc-element');

            this.$emit('stripeElement', card);
        },

        methods: {
            focusStripeElement(element) {
                this.focused = element;
            },
            blurStripeElement() {
                this.focused = null;
            }
        },

        watch: {
            paymentType(value) {
                this.$emit('paymentType', value)
            }
        }
    }
</script>

<style lang="scss">
    @import "~bootstrap/scss/functions";
    @import "~bootstrap/scss/variables";
    @import "~bootstrap/scss/mixins";

    .payment-type {
        margin-bottom: 15px;
        margin-left: 15px;

        display: flex;
        flex-direction: column;
        align-items: flex-start;

        @include media-breakpoint-up('md') {
            margin-left: 0;
            flex-direction: row;
            align-items: center;
            justify-content: center;
        }

        &__label {
            margin: 5px;
            display: flex;
            flex-direction: row;
            align-items: center;
            justify-content: center;
        }

        &__input {
            margin-right: 5px;
        }

        &__image {
            max-height: 150px;
            max-width: 150px;
            margin: auto
        }

        &__select {
            width: 42% !important;
        }
    }

    .StripeElement {
        width: 100%;
        margin-bottom: 14px;
        background-color: white;
        border: 1px solid #e6e7ea;
        padding: 10px;

        &--focus {
            border-color: #f60;
        }
    }

    .stripe-container {
        @include media-breakpoint-up('lg') {

            display: grid;
            grid-template-columns: 70% 1fr;
            grid-auto-rows: 1fr;

            align-items: center;
            justify-items: center;
        }

        &__form {
            width: 100%;
            @include media-breakpoint-up('lg') {
                padding: 0 30px 0 45px;
                border-right: 1px solid #ddd;
            }
        }

        &__secure {
            display: none;

            @include media-breakpoint-up('lg') {
                display: block;
            }
        }
    }
</style>
