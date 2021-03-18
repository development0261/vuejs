<template>
    <div>
        <div class="checkout-form__form-title">
            <h2 v-text="label"/>
        </div>
        <div class="checkout-form__form-wrapper">
            <input type="text" class="checkout-form__form-input input" placeholder="Phone*" name="phone" v-model="phone"
                   required>
            <div class="form-error" v-if="errors.phone" v-text="errors.phone"/>
            <select name="country" class="select checkout-form__form-input" v-model="country" required>
                <option value="" disabled selected>Country:</option>
                <option value="US">United States</option>
                <option value="Canada">Canada</option>
                <template v-if="! extended">
                    <option value="UK">United Kingdom</option>
                    <option value="Australia">Australia</option>
                    <option value="Other">Other</option>
                </template>
            </select>
            <div class="form-error" v-if="errors.country" v-text="errors.country"/>
            <template v-if="extended">
                <input type="text" placeholder="Address*" name="address" class="input checkout-form__form-input"
                       required>
                <div class="form-error" v-if="errors.address" v-text="errors.address"/>
                <input type="text" placeholder="City*" name="city" class="input checkout-form__form-input" required>
                <div class="form-error" v-if="errors.city" v-text="errors.city"/>
            </template>
            <select name="province" class="select checkout-form__form-input" v-if="country === 'Canada'" required>
                <option disabled selected>Province:</option>
                <option value="Alberta">Alberta</option>
                <option value="British Columbia">British Columbia</option>
                <option value="Manitoba">Manitoba</option>
                <option value="New Brunswick">New Brunswick</option>
                <option value="Newfoundland and Labrador">Newfoundland and Labrador</option>
                <option value="Nova Scotia">Nova Scotia</option>
                <option value="Ontario">Ontario</option>
                <option value="Prince Edward Island">Prince Edward Island</option>
                <option value="Quebec">Quebec</option>
                <option value="Saskatchewan">Saskatchewan</option>
            </select>
            <div class="form-error" v-if="errors.province && country === 'Canada'" v-text="errors.province"/>
            <temaplte v-if="extended">
                <select name="province" class="select checkout-form__form-input" v-if="country === 'US'" required>
                    <option disabled selected>State:</option>
                    <option value="Alabama">Alabama</option>
                    <option value="Alaska">Alaska</option>
                    <option value="Arizona">Arizona</option>
                    <option value="Arkansas">Arkansas</option>
                    <option value="California">California</option>
                    <option value="Colorado">Colorado</option>
                    <option value="Connecticut">Connecticut</option>
                    <option value="Delaware">Delaware</option>
                    <option value="Florida">Florida</option>
                    <option value="Georgia">Georgia</option>
                    <option value="Hawaii">Hawaii</option>
                    <option value="Idaho">Idaho</option>
                    <option value="Illinois">Illinois</option>
                    <option value="Indiana">Indiana</option>
                    <option value="Iowa">Iowa</option>
                    <option value="Kansas">Kansas</option>
                    <option value="Kentucky">Kentucky</option>
                    <option value="Louisiana">Louisiana</option>
                    <option value="Maine">Maine</option>
                    <option value="Maryland">Maryland</option>
                    <option value="Massachusetts">Massachusetts</option>
                    <option value="Michigan">Michigan</option>
                    <option value="Minnesota">Minnesota</option>
                    <option value="Mississippi">Mississippi</option>
                    <option value="Missouri">Missouri</option>
                    <option value="Montana">Montana</option>
                    <option value="Nebraska">Nebraska</option>
                    <option value="Nevada">Nevada</option>
                    <option value="New Hampshire">New Hampshire</option>
                    <option value="New Jersey">New Jersey</option>
                    <option value="New Mexico">New Mexico</option>
                    <option value="New York">New York</option>
                    <option value="North Carolina">North Carolina</option>
                    <option value="New Dakota">New Dakota</option>
                    <option value="Ohio">Ohio</option>
                    <option value="Oklahoma">Oklahoma</option>
                    <option value="Oregon">Oregon</option>
                    <option value="Pennsylvania">Pennsylvania</option>
                    <option value="Rhode Island">Rhode Island</option>
                    <option value="South Carolina">South Carolina</option>
                    <option value="South Dakota">South Dakota</option>
                    <option value="Tennessee">Tennessee</option>
                    <option value="Texas">Texas</option>
                    <option value="Utah">Utah</option>
                    <option value="Vermont">Vermont</option>
                    <option value="Virginia">Virginia</option>
                    <option value="Washington">Washington</option>
                    <option value="West Virginia">West Virginia</option>
                    <option value="Wisconsin">Wisconsin</option>
                    <option value="Wyoming">Wyoming</option>
                </select>
                <div class="form-error" v-if="errors.province && country === 'US'" v-text="errors.province"/>
                <input type="text" placeholder="Zip/Postal Code*" class="input checkout-form__form-input" v-if="country"
                       name="zip" required>
                <div class="form-error" v-if="errors.zip" v-text="errors.zip"/>
            </temaplte>
        </div>
    </div>
</template>

<script>
    export default {
        name: "CheckoutBillingInfo",
        props: {
            label: {
                type: String,
                required: true
            },
            extended: {
                type: Boolean,
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
                country: this.oldInput.country || '',
                phone: this.oldInput.phone || '',
                address: this.oldInput.address || '',
                city: this.oldInput.city || '',
                province: this.oldInput.province || '',
                zip: this.oldInput.zip || '',
            }
        },

    }
</script>
