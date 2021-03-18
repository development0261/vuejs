<template>
    <div class="calculator">
        <template v-for="(step, index) in calculator.steps">
            <div class="calculator__step" :class="[step.class || '']">
                <div class="calculator__step-title">
                    <div class="calculator__step-number" v-text="index + 1"/>
                    <h2 class="calculator__step-title-text" v-text="step.title"/>
                </div>
                <template v-if="step.type !== 'button'">
                    <select class="calculator__step-control select"
                            @change="resetSelected(index)"
                            :size="step.size || 1"
                            v-model="selected[index]">
                        <template v-if="index === 0">
                            <option v-for="option in Object.keys(calculator.values)" :value="option"
                                    v-text="option"></option>
                        </template>
                        <template v-else>
                            <option v-for="(option, value) in steps[index - 1]"
                                    :value="typeof option === 'string' ? option : value"
                                    v-text="value"></option>
                        </template>
                    </select>
                </template>
                <template v-else>
                    <button class="button button--secondary"
                            @click="$modal.show('calculator')">
                        Submit
                    </button>
                </template>
            </div>
        </template>

        <BaseModal name="calculator" modal-width="800px">
            <template #header>
                <div class="calculator__modal-header">
                    <LazyImg src="/images/modalheader.gif"/>
                    Almost there: complete this form and click the button below to gain instant access.
                </div>
            </template>
            <template #body>
                <div class="calculator__modal-body">
                    <form method="post" @submit="loading=true">
                        <template v-if="loading">
                            <div style="margin-bottom: 20px">One moment please... Busy
                                calculating
                            </div>
                            <div class="loader"/>
                        </template>
                        <div v-show="!loading">
                            <slot v-show="!loading"/>
                            <input type="hidden" name="result" v-model="grade">
                            <input class="input calculator__modal-body-input" name="email" type="email"
                                   placeholder="E-mail" required>
                            <button class="button button--secondary calculator__modal-body-button"
                                    v-text="buttonText"></button>
                        </div>
                    </form>
                </div>

            </template>
            <template #footer>
                <div class="calculator__modal-footer">
                    <LazyImg src="/images/lock-icon.png" class="calculator__modal-footer-img"/>
                    Your email address will remain 100% safe & confidential.
                </div>
            </template>
        </BaseModal>
    </div>
</template>

<script>
    import amcas from '../../classes/Amcas';
    import medSchool from '../../classes/Msac';
    import lsac from '../../classes/Lsac';
    import omsas from '../../classes/Omsas';

    export default {
        name: "Calculator",

        props: {
            buttonText: {
                type: String,
                required: true
            },
            type: {
                type: String,
                required: true
            }
        },

        data() {
            const calculators = {
                amcas,
                medSchool,
                lsac,
                omsas,
                olsas: omsas
            };

            const calculator = calculators[this.type];
            const calculatorValues = calculator.values;
            const selected = [];

            const initialKey = Object.keys(calculatorValues)[0];
            selected.push(initialKey);
            let level = calculator.values[initialKey];
            for (let i = 1; i < calculator.steps.length; i++) {
                if (calculator.steps[i].type !== 'button') {
                    let key = Object.values(level)[0];
                    if (typeof key !== 'string') {
                        key = Object.keys(level)[0];
                    }
                    selected.push(key);
                    level = level[key];
                }
            }

            return {
                calculator,
                selected,
                loading: false
            }
        },

        methods: {
            resetSelected(index) {
                let level = this.calculator.values[this.selected[0]];
                for (let i = 1; i < index + 1; i++) {
                    level = level[this.selected[i]];
                }
                for (let i = index + 1; i < this.calculator.steps.length; i++) {
                    if (this.calculator.steps[i].type !== 'button') {
                        let key = Object.values(level)[0];
                        if (typeof key !== 'string') {
                            key = Object.keys(level)[0];
                        }
                        this.$set(this.selected, i, key);
                        level = level[key];
                    }
                }
            }
        },

        computed: {
            grade() {
                return this.selected[this.selected.length - 1];
            },
            steps() {
                return [
                    this.calculator.values[this.selected[0]],
                    this.calculator.values[this.selected[0]][this.selected[1]],
                ]
            }
        }
    }
</script>
