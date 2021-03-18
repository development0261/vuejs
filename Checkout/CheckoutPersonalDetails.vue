<template>
    <div>
        <div class="checkout-form__form-title">
            <h2 v-text="label"/>
        </div>
        <div class="checkout-form__form-wrapper">
            <input type="text" v-model="firstName" class="checkout-form__form-input input" placeholder="First Name*"
                   name="firstName" required>
            <div class="form-error" v-if="errors.firstName" v-text="errors.firstName"/>
            <input type="text" v-model="lastName" class="checkout-form__form-input input" placeholder="Last Name*"
                   name="lastName" required>
            <div class="form-error" v-if="errors.lastName" v-text="errors.lastName"/>
            <input type="email" v-model="email" class="checkout-form__form-input input" placeholder="Email*"
                   name="email" required>
            <div class="form-error" v-if="errors.email" v-text="errors.email"/>
            <button type="button" class="button button--secondary checkout-form__form-button" v-text="buttonText"
                    @click="submit"/>
            <div class="checkout-form__form-satisfaction">100% SATISFACTION GUARANTEE</div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "CheckoutPersonalDetails",
        props: {
            label: {
                type: String,
                required: true
            },
            buttonText: {
                type: String,
                required: true
            },
            plan: {
                type: String,
                required: true
            },
            errors: {
                type: Object,
                required: true
            },
            oldInput: {
                type: Object,
                required: true
            }
        },

        data() {
            return {
                saved: false,
                firstName: this.oldInput.firstName || '',
                lastName: this.oldInput.lastName || '',
                email: this.oldInput.email || '',
            }
        },

        methods: {
            submit() {
                const inputs = this.$el.querySelectorAll('input');
                for (let input in inputs) {
                    if (typeof inputs[input].reportValidity === 'function' && !inputs[input].reportValidity()) {
                        return false;
                    }
                }

                this.saveStep();
                this.$emit('nextStep');
            },

            async saveStep() {
                if (this.saved) {
                    return;
                }

                let waiverUrl = '/checkout-save-step-one' + window.location.pathname;

                if(this.plan){
                    waiverUrl += `/${this.plan}`
                }

                try {
                    await axios.post(waiverUrl, {
                        firstName: this.firstName,
                        lastName: this.lastName,
                        email: this.email,
                    });
                    this.saved = true;
                } catch (error) {
                    console.log(error);
                    setTimeout(this.saveStep, 30000);
                }
            }
        }
    }
</script>
