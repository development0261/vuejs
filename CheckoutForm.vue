<template>
    <div class="checkout-form">
        <CheckoutLabels :labels="stepLabels" :step="step"/>
        <form method="post" :action="url" class="checkout-form__form" ref="form"
              :class="{' was-validated': errors.length }">
            <div class="message message--error" v-if="exception" v-text="exception"/>
            <input type="hidden" name="_token" :value="csrf">
            <CheckoutPersonalDetails v-show="step === 0" :label="personalInformationLabel" :button-text="stepButtons[0]"
                                     :plan="plan" :errors="errors" :old-input="oldInput"
                                     @nextStep="showStep2"/>
            <template v-if="step === 1">

                <CheckoutBillingInfo :label="billingInformationLabel" :extended="deliveryAddress"
                                     :errors="errors" :old-input="oldInput"/>
                <hr class="divider">
                <div class="message message--error message--stripe" v-if="stripeError" v-text="stripeError"/>
                <CheckoutPayment :paypal="paypal" @paymentType="changePaymentType"
                                 :price="`${currency.symbol}${price.toFixed(2)}`"
                                 @stripeElement="stripeElement = $event">
                    <Installments v-if="installments.enabled" :options="installments" :price="price.toFixed(2)"
                                  :currency="currency" @installments="installmentsToggled = $event"/>
                </CheckoutPayment>
                <CheckoutButton :price="totalPrice" :currency="currency" :label="summaryLabel" :plan="plan"
                                :trial-text="trialText" :product-text="productText"
                                :installments="installmentsToggled ? parseInt(installments.number) : 1"
                                :button-text="stepButtons[1]" @submit="submit" :submitting="submitting">
                    <template #upsells>
                        <Upsells v-if="upsells.length" :items="upsells" v-model="selectedUpsells"/>
                    </template>
                </CheckoutButton>
            </template>
            <input type="hidden" v-model="token" name="token">
        </form>
    </div>
</template>

<script>
    import CheckoutLabels from "./Checkout/CheckoutLabels";
    import CheckoutPersonalDetails from "./Checkout/CheckoutPersonalDetails";
    import CheckoutBillingInfo from "./Checkout/CheckoutBillingInfo";
    import CheckoutPayment from "./Checkout/CheckoutPayment";
    import CheckoutButton from "./Checkout/CheckoutButton";
    import Installments from "./Checkout/Installments";
    import Upsells from "./Checkout/Upsells";

    export default {
        name: "CheckoutForm",
        components: {
            Installments,
            CheckoutButton, CheckoutPayment, CheckoutBillingInfo, CheckoutPersonalDetails, CheckoutLabels, Upsells
        },
        props: {
            stepLabels: {
                type: Array,
                required: true
            },
            personalInformationLabel: {
                type: String,
                required: true
            },
            billingInformationLabel: {
                type: String,
                required: true
            },
            summaryLabel: {
                type: String,
                required: true
            },
            stepButtons: {
                type: Array,
                required: true
            },
            deliveryAddress: {
                type: Boolean,
                default: false
            },
            currency: {
                type: Object,
                required: true
            },
            price: {
                type: Number,
                required: true
            },
            plan: {
                type: String,
                required: true
            },
            errors: {
                type: Object,
                default() {
                    return {};
                }
            },
            oldInput: {
                type: Object,
                default() {
                    return {};
                }
            },
            exception: {
                type: String,
                default: ''
            },
            paypal: {
                type: Boolean,
                default: true
            },
            productText: {
                type: String,
                default: ''
            },
            trialText: {
                type: String,
                default: ''
            },
            upsells: {
                type: Array,
                default() {
                    return [];
                }
            },
            installments: {
                type: Object,
                default: {}
            }

        },

        data() {
            return {
                csrf: window.token,
                step: 0,
                paymentType: 'stripe',
                stripeElement: null,
                token: null,
                submitting: false,
                selectedUpsells: [],
                installmentsToggled: 0,
                url: window.location.href.split('?')[0] + (this.getInvoice() ? `?invoice=${this.getInvoice()}` : ''),
                stripeError: null
            }
        },

        methods: {
            getInvoice() {
                let match;
                if (match = (new RegExp('[?&]' + encodeURIComponent('invoice') + '=([^&]*)')).exec(location.search)) {
                    return decodeURIComponent(match[1]);
                }
                return false;
            },


            showStep2() {
                document.body.dispatchEvent(new CustomEvent('checkout-step-2-opened', { bubbles: true }));
                if (typeof CHECKOUT_REFERSION !== "undefined" && typeof _refersion !== 'undefined') {
                    _refersion(() => {
                        _rfsn._addCart(CHECKOUT_REFERSION);
                    });
                }

                this.step++;
            },

            changePaymentType(type) {
                this.paymentType = type;
            },

            async submit() {
                if (!this.validateForm()) {
                    return;
                }

                if (this.paymentType === 'stripe') {
                    const stripeDone = await this.generateStripeInfo();
                    if (!stripeDone) {
                        this.scrollToError(".message--stripe");
                        return;
                    }
                }

                this.$refs.form.submit();
            },

            validateForm() {
                const inputs = this.$el.querySelectorAll('input, select');
                for (let input in inputs) {
                    if (typeof inputs[input].reportValidity === 'function' && !inputs[input].reportValidity()) {

                        return false;
                    }
                }
                return true;
            },

            async generateStripeInfo() {
                this.submitting = true;
                this.stripeError = null;
                try {
                    const {paymentMethod, error} = await stripe.createPaymentMethod(
                        'card', this.stripeElement
                    );
                    if (error) {
                        this.stripeError = error.message;
                    } else {
                        this.token = paymentMethod.id;
                        return true;
                    }
                } catch (error) {

                }
                this.submitting = false;
                return false;
            },

            scrollToError(errorElement) {
                var element = document.querySelector(errorElement);
                window.scrollTo(0, element.offsetTop - 110);
            }
        },

        computed: {
            totalPrice() {
                let price = this.price;
                if (this.installmentsToggled) {
                    price = this.installments.price;
                    if (this.installments.discount) {
                        price = this.installments.discountPrice;

                    }
                    price = parseFloat(price) / parseFloat(this.installments.number) + parseFloat(this.installments.fee);
                }
                this.selectedUpsells.forEach((key) => {
                    price += parseFloat(this.upsells[key].price);
                });
                return price;
            }
        }
    }
</script>

<style lang="scss">
    @import "~bootstrap/scss/functions";
    @import "~bootstrap/scss/variables";
    @import "~bootstrap/scss/mixins";

    .checkout-form {
        padding-top: 10px;

        &__form {
            margin-top: 20px;
            text-align: center;
            border: 1px solid #eee;
            background-color: #f1f1f1;

            &-wrapper {
                padding: 14px 15px;

                @include media-breakpoint-up('lg') {
                    padding: 14px 35px;
                }
            }

            &-title {
                border-radius: 5px 5px 0 0;
                padding: 22px 0;
                color: #fff;
                background-color: #006;
                font-weight: 400 !important;
            }

            &-input {
                width: 85%;
                line-height: 1.7;
                font-size: 13.5px;
                color: #646464;
                padding: 10px;
                font-weight: 300;
                margin-bottom: 14px;
            }

            &-button {
                margin: 20px auto 10px;
                padding: 21px;
                font-size: 36px;
                font-weight: 600;
                width: 100%;
            }

            &-satisfaction {
                font-size: 1.1em;
            }
        }
    }

    .divider {
        margin: 1em auto 1.6em;
        height: 1px;
        width: 74%;
    }

    .form-error {
        margin-top: -14px;
        font-size: 80%;
        color: #dc3545;
        text-align: left;
        width: 80%;
        margin-left: 8.5%;
    }

    .message--error {
        background-color: #f8d7da;
        border-color: #f5c6cb;
    }
</style>
