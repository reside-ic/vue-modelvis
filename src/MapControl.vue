<template>
    <l-control position="topright">
        <div style="width: 265px;" class="p-3 map-control">
            <form>
                <div v-if="showIndicators" class="row form-group">
                    <label class="col-3 col-form-label">{{indicatorLabel}}</label>
                    <div class="col">
                        <tree-select :value="indicator"
                                     :multiple="false"
                                     :clearable="false"
                                     :searchable="false"
                                     :options="indicatorOptions"
                                     @input="indicatorChanged"></tree-select>
                    </div>
                </div>
                <div class="row form-group">
                    <label class="col-3 col-form-label">{{detailLabel}}</label>
                    <div class="col">
                        <tree-select v-model="detail"
                                     :multiple="false"
                                     :clearable="false"
                                     :searchable="false"
                                     :options="detailOptions"
                                     @input="$emit('detail-changed', detail)"></tree-select>
                    </div>
                </div>
            </form>
        </div>
    </l-control>
</template>

<script lang="ts">
    import Vue from "vue";
    import TreeSelect from '@riophae/vue-treeselect'
    import {LControl} from 'vue2-leaflet';
    import {LevelLabel, IndicatorMetadata} from "./types";

    interface Data {
        detail: any;
        optionsLoaded: boolean;
    }

    interface Props {
        indicatorLabel: string,
        detailLabel: string,
        indicator: string,
        initialDetail: number,
        showIndicators: boolean,
        indicatorsMetadata: IndicatorMetadata[],
        levelLabels: LevelLabel[]
    }

    interface Option {
        id: any;
        label: string;
    }

    interface Computed {
        detailOptions: Option[]
        indicatorOptions: Option[]
    }

    interface Methods {
        indicatorChanged: (newVal: string) => void;
    }

    export default Vue.extend<Data, Methods, Computed, Props>({
        name: 'MapControl',
        components: {
            TreeSelect,
            LControl
        },
        props: {
            indicatorLabel: String,
            detailLabel: String,
            indicator: String,
            initialDetail: Number,
            showIndicators: Boolean,
            indicatorsMetadata: Array,
            levelLabels: Array
        },
        data(): Data {
            return {
                optionsLoaded: false,
                detail: this.initialDetail
            }
        },
        computed: {
            detailOptions: function(){
                return this.levelLabels.filter(l => l.display).map(l => {
                    return {id: l.id, label: l.area_level_label}
                });
            },
            indicatorOptions: function() {
                return this.indicatorsMetadata.map((i: IndicatorMetadata) => { return {id: i.indicator, label: i.name}; });
            }
        },
        methods: {
            indicatorChanged: function (newVal: string) {
                this.$emit("indicator-changed", newVal);
            }
        }
    });
</script>