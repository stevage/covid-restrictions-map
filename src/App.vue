<template lang="pug">
    #app.flex.flex-column.vh-100.avenir
        //- #top.bb.b--gray.bg-washed-yellow
        //-     h1 Community Map
        #middle.flex.flex-auto
            //- #sidebar.br.b--light-gray.overflow-auto.w3.w-25-ns.pa2
            #map-container.relative.flex-auto
                Map
                #overlay.absolute
                    //- #legend
        #bottom.bt.b--light-gray.flex-none
</template>

<script>
import Map from './components/Map.vue';
import FeatureInfo from './components/FeatureInfo.vue';
import { EventBus } from './EventBus';
// import flatten from 'array-flatten';
window.app = {};
import initTest from './initTest.json';
import flat from 'flat';
export default {
    name: 'app',
    components: {
        Map,
        FeatureInfo,
    },
    data() {
        return {
            csvUrl: undefined,
            error: undefined,
        };
    },
    created() {
        window.app.App = this;
        EventBus.$on('error', e => (this.error = e));
    },
    computed: {
        testItems() {
            const flatObj = flat(initTest);
            return Object.keys(flatObj)
                .filter(k => flatObj[k] === 'csv')
                .map(k => {
                    const base = k.replace(/\.[^.]*$/, '');
                    return {
                        name: flatObj[`${base}.name`],
                        url:
                            'https://raw.githubusercontent.com/TerriaJS/nationalmap/master/wwwroot/' +
                            flatObj[`${base}.url`],
                    };
                });
        },
    },
    methods: {
        loadCsv(url) {
            this.error = null;
            EventBus.$emit('load-csv', url || this.csvUrl);
        },
    },
};

require('tachyons/css/tachyons.min.css');
</script>

<style>
html,
body {
    height: 100vh;
    width: 100%;
    margin: 0;
    padding: 0;
}
#sidebar {
    min-width: 200px;
}
</style>
