<template>
    <div>
        <v-select ref="vueSelect" v-model="mutableValue" :multiple="multiple" :dir="dir" :searchable="searchEnabled" :options="options" v-on="$listeners" v-bind="$attrs">
            <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope">
                <slot :name="slot" v-bind="scope"/>
            </template>
        </v-select>
    </div>
</template>

<script>
    export default {
        components: {
            'v-select': require('vue-select').default
        },
        props: {
            value: null,
            options: {
                type: Array,
                default: () => []
            },
            multiple: {
                type: Boolean,
                default: false
            },
            searchEnabled: {
                type: Boolean,
                default: true
            }
        },
        data() {
            return {
                mutableValue: null
            };
        },
        created () {
            if (this.multiple) {
                this.mutableValue = [];
            }
        },
        mounted () {
            this.mutableValue = this.value;
        },
        watch: {
            mutableValue (newValue, oldValue) {
                if (newValue instanceof Array) {
                    var value = [];
                    for (var i in newValue) {
                        value.push(newValue[i].value);
                    }
                    if (value.filter((i) => !this.value.includes(i)).length != 0) {
                        this.$emit("input", value);
                    }
                } else if (newValue != null && typeof newValue.value !== 'undefined') {
                    this.$emit("input", newValue.value);
                } else {
                    this.$emit("input", null);
                }
            },
            value (newValue, oldValue) {
                this.mutableValue = this.mapValue(newValue);
                this.$forceUpdate();
            },
            options (newValue, oldValue) {
                this.mutableValue = [];
                this.mutableValue = this.mapValue(this.value);
            }
        },
        computed: {
            dir() {
                return document.dir;
            }
        },
        methods: {
            mapValue(newValue) {
                var value = null;
                if (this.multiple) {
                    value = [];
                    if (newValue instanceof Array) {
                        for (var i in this.options) {
                            if (newValue.includes(this.options[i].value)) {
                                value.push(this.options[i]);
                            }
                        }
                    }
                } else {
                    if (!(newValue instanceof Array)) {
                        for (var i in this.options) {
                            if (this.options[i].value == newValue) {
                                value = this.options[i];
                            }
                        }
                    }
                }
                return value;
            }
        },
    }
</script>
