<template lang="pug">
#map.absolute.absolute--fill
</template>

<script>
import mapboxgl from 'mapbox-gl/dist/mapbox-gl-dev.js';
import U from 'mapbox-gl-utils';
import { sheets2geojson } from 'sheets2geojson';
import { EventBus } from '../EventBus';
import { addCsvByUrl, addCsvByRows } from './csv-geo-au';

const lgas = `Banyule (C)
Bayside (C)
Boroondara (C)
Brimbank (C)
Cardinia (S)
Casey (C)
Greater Dandenong (C)
Darebin (C)
Frankston (C)
Glen Eira (C)
Hobsons Bay (C)
Hume (C)
Kingston (C)
Knox (C)
Manningham (C)
Maribyrnong (C)
Maroondah (C)
Melbourne (C)
Melton (C)
Monash (C)
Moonee Valley (C)
Moreland (C)
Mornington Peninsula (S)
Nillumbik (S)
Port Phillip (C)
Stonnington (C)
Whitehorse (C)
Whittlesea (C)
Wyndham (C)
Yarra (C)
Yarra Ranges (S)
Mitchell (S)`.split('\n');

export default {
    async mounted() {
        // replace this Mapbox access token with your own
        // mapboxgl.accessToken = 'pk.eyJ1Ijoic3RldmFnZSIsImEiOiJGcW03aExzIn0.QUkUmTGIO3gGt83HiRIjQw';
        mapboxgl.accessToken =
            'pk.eyJ1Ijoic2JjaGVycmUiLCJhIjoiY2s2cTkxcHVxMXVnNzNsbDl2bTN1Mm83dyJ9.sU2NJpziux-m0MCCkm41iQ';
        const map = new mapboxgl.Map({
            container: 'map',
            center: [144.96, -37.81],

            zoom: 5,
            style: 'mapbox://styles/mapbox/outdoors-v11',
            // style: { version: 8, sources: {}, layers: []},
            hash: true,
        });
        U.init(map, mapboxgl);
        window.map = map;
        window.app.Map = this;

        const corsProxy = 'https://cors-anywhere.herokuapp.com';
        map.U.onLoad(() => {
            // addCsvByUrl(map, 'https://raw.githubusercontent.com/TerriaJS/terriajs/master/wwwroot/test/csv/CED_2018_CED_CODE18.csv');
            // addCsvByUrl(map, 'https://raw.githubusercontent.com/TerriaJS/terriajs/master/wwwroot/test/csv/SED_2016_SED_CODE16.csv');
            // addCsvByUrl(map, 'https://raw.githubusercontent.com/TerriaJS/terriajs/master/wwwroot/test/csv/55k-SA1s.csv', 'Random');
            // addCsvByUrl(map, '55k-SA1s.csv', 'Random');
            // addCsvByUrl(map, 'https://raw.githubusercontent.com/TerriaJS/nationalmap/master/wwwroot/test/csv-geo-au/sos.csv');
            // addCsvByUrl(map, 'https://raw.githubusercontent.com/TerriaJS/nationalmap/master/wwwroot/test/csv-geo-au/phn.csv');
            // addCsvByUrl(map, 'https://raw.githubusercontent.com/TerriaJS/terriajs/master/wwwroot/test/csv/3000s.csv', 'Value');

            1 &&
                addCsvByRows(
                    map,
                    // `${corsProxy}/https://docs.google.com/spreadsheets/d/e/2PACX-1vRhziJoNYYerN82sIQ0QewqY0ARnC0Dv7FvxtnU-aSYypzID1iO8RNujvjNRjsjYntSV1ePHWj2RwTJ/pub?gid=0&single=true&output=csv`,
                    // 'Restriction',
                    lgas.map(lga => ({ LGA_NAME_2011: lga, Restriction: 3 })),
                    'Restriction',
                    {
                        colorScheme: 'BuGn',
                        source: { maxzoom: 13 },
                        sourceId: 'lgas',
                        layerId: 'lgas',
                        paint: {
                            'fill-opacity': 0.2,
                        },
                    },
                );
            1 &&
                addCsvByRows(
                    map,
                    [
                        3012,
                        30221,
                        3032,
                        3038,
                        3042,
                        3046,
                        3047,
                        3055,
                        3060,
                        3064,
                        3031,
                        3051,
                    ].map(POA => ({ POA: String(POA), Restriction: 4 })),
                    'Restriction',
                    {
                        colorScheme: 'PuRd',
                        paint: {
                            'fill-opacity': 0.2,
                        },
                        sourceId: 'poas',
                        layerId: 'poas',
                    },
                );
            // TODO add states
            // 1 &&
            //     addCsvByRows(
            //         map,
            //         [
            //             'NSW'
            //         ].map(POA => ({ POA: String(POA), Restriction: 4 })),
            //         'Restriction',
            //         {
            //             colorScheme: 'PuRd',
            //             paint: {
            //                 'fill-opacity': 0.5,
            //             },
            //             sourceId: 'poas',
            //             layerId: 'poas',
            //         },
            //     );
        });
        EventBus.$on('load-csv', csvUrl => {
            // const rawUrl = csvUrl.replace('https://github.com/TerriaJS/nationalmap/blob/master/wwwroot/test/csv-geo-au/sos.csv', https://raw.githubusercontent.com/TerriaJS/nationalmap/master/wwwroot/test/csv-geo-au/sos.csv
            const rawUrl = csvUrl
                .replace(/github\.com/, 'raw.githubusercontent.com')
                .replace(/blob\//, '');
            map.U.removeSource('choropleth');
            map.U.removeSource('points');

            try {
                addCsvByUrl(map, rawUrl);
            } catch (e) {
                EventBus.$emit('error', e);
            }
        });
    },
};
import 'mapbox-gl/dist/mapbox-gl.css';
</script>

<style scoped>
</style>
