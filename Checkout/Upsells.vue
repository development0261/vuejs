<template>
    <div>
        <div v-for="(item, key) in items" class="upsell">
            <label class="upsell__checkbox">
                <img class="upsell__icon" src="/images/orange-arrow.gif" v-if="item.hideOrangeArrow !== 'on'">
                <input type="checkbox" :value="key" name="upsell[]" v-model="selectedItems">
                <span v-text="item.text"/>
                <Tooltip>
                    <template #trigger>
                        <FontAwesomeIcon icon="info-circle"/>
                    </template>
                    <template #content>
                        <div v-html="item.lightBoxText"></div>
                    </template>
                </Tooltip>
            </label>
            <span v-html="item.areaText"/>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Upsells",
        props: {
            value: {
                type: Array,
                required: true
            },
            items: {
                type: Array,
                required: true
            }
        },

        data() {
            return {
                selectedItems: [],
            }
        },

        watch: {
            selectedItems(value) {
                this.$emit('input', value);
            }
        }
    }
</script>

<style lang="scss" scoped>
    .upsell {
        text-align: left;
        margin: 27px 0 30px;
        border: 1px solid #006;
        background-color: #e9e9e9;
        padding: 15px;
        border-radius: 3px;

        &__checkbox {
            display: block;
            cursor: pointer;
            background-color: #00c0f0;
            border-radius: 3px;
            padding: 20px 20px;
            text-align: center;
            color: #fff;
        }

        &__icon {
            height: 17px;
            vertical-align: middle;
        }
    }

</style>
