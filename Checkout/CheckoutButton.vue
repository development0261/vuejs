<template>
    <div>
        <div class="checkout-form__form-title">
            <h2 v-text="label"/>
        </div>
        <div class="checkout-form__form-wrapper">
            <slot name="upsells"/>
            <div class="promo-field">
                <input type="hidden" v-if="couponStatus === 'applied'" v-model="coupon" name="coupon">
                <input type="text" class="input checkout-form__form-input promo-field__input" placeholder="Promo Code"
                       :readonly="couponStatus === 'applied'"
                       v-model="coupon">
                <button type="button" class="button promo-field__input" @click="couponButton"
                        :disabled="!coupon || couponStatus === 'loading'">
                    <FontAwesomeIcon icon='spinner' spin v-if="couponStatus === 'loading'"/>
                    <span v-if="! couponStatus">APPLY</span>
                    <span v-if="couponStatus === 'applied'">CLEAR</span>
                </button>
            </div>
            <div class="checkout-price">
                <b v-text="`Your Order Total (${currency.currency}):`"/>
                <span v-if="! trialText" v-text="`${currency.symbol}${displayPrice}`"/>
                <span v-else class="text-right">
                    <span v-text="trialText.replace('[price]',`${currency.symbol}${displayPrice}`)"/>
                    <br>
                    <span v-text="productText"/>
                </span>
            </div>

            <template v-if="! submitting">
                <div class="terms-text">
                    By clicking the button below, I agree with <a class="terms-text__link" href="/Disclaimer-Privacy-Cancellation.html" target="_blank">Terms of
                    Service</a> and <a class="terms-text__link" href="/privacy-policy"
                                       target="_blank">Privacy Policy</a>.
                </div>
                <button type="button" class="button button--secondary checkout-form__form-button" v-text="buttonText"
                        @click="$emit('submit')"/>
                <div class="checkout-form__form-satisfaction">100% SATISFACTION GUARANTEE</div>
            </template>
            <template v-else>
                <h2 class="loading-text">Processing your enrollment. Please wait and do NOT refresh or close this
                    window! It is normal
                    for the system to
                    take up to 20 seconds.</h2>
                <div class="loader"></div>
            </template>
        </div>
    </div>
</template>

<script>

    export default {
        name: "CheckoutButton",
        props: {
            label: {
                type: String,
                required: true
            },

            buttonText: {
                type: String,
                required: true
            },

            price: {
                type: Number,
                required: true
            },

            currency: {
                type: Object,
                required: true
            },
            plan: {
                type: String,
                required: true
            },
            submitting: {
                type: Boolean,
                required: true
            },
            productText: {
                type: String,
                default: ''
            },
            trialText: {
                type: String,
                default: ''
            },
            installments: {
                type: Number,
                default: 1
            }
        },

        data() {
            return {
                coupon: '',
                couponStatus: null,
                discount: null
            }
        },

        computed: {
            displayPrice() {
                let price = this.price.toFixed(2);
                let discount = 0;
                if (this.discount) {
                    if (this.discount.type === 'fixed') {
                        discount = parseFloat(this.discount.amount) / this.installments;
                    } else {
                        discount = price * parseFloat(this.discount.amount) / 100;
                    }
                }
                price = Math.max(price - discount.toFixed(2), 0);
                return price.toFixed(2);
            }
        },

        methods: {
            clearCoupon() {
                this.coupon = '';
                this.couponStatus = null;
                this.discount = null;
            },
            async applyCoupon() {
                this.couponStatus = 'loading';
                try {
                    let url = `/coupon/${this.coupon}${window.location.pathname}`;
                    if (this.plan) {
                        url += `/${this.plan}`
                    }
                    const response = await axios.get(url);
                    if (response.status > 199 && response.status < 300) {
                        this.discount = response.data;
                        this.couponStatus = 'applied';
                        return;
                    }
                } catch (error) {

                }
                alert('Invalid Coupon');
                this.clearCoupon();
            },
            couponButton() {
                if (!this.couponStatus) {
                    this.applyCoupon();
                } else {
                    this.clearCoupon()
                }
            }
        }

    }
</script>

<style lang="scss" scoped>

    @import "~bootstrap/scss/functions";
    @import "~bootstrap/scss/variables";
    @import "~bootstrap/scss/mixins";

    .promo-field {
        display: flex;
        width: 85%;
        margin: auto;

        &__input {
            display: inline;
            margin: 0;
        }
    }

    .checkout-price {

        margin: 20px 0;

        display: flex;
        justify-content: space-between;
        font-size: 1.2em;

        @include media-breakpoint-up('md') {
            margin: 20px -20px;
        }
    }

    .terms-text {
        line-height: 25px;
        vertical-align: top;
        font-size: 12px;

        &__link {
            text-decoration: underline;
            cursor: pointer;
        }
    }

    .loading-text {
        margin-bottom: 1em;
        font-weight: 400;
        color: #006;
        font-size: 1.63em;
        font-family: Lato, arial !important;
    }

</style>
