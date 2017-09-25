# V-Radar

[![global](https://www.nicolas-gomes.com/assets/images/posts/2017-09-24/vue-radar-screenshot.png)](http://vue-radar.nicolas-gomes.com)

This is a Vue component, you can create a radar diagramm with it.

## How to install it:

`npm install v-radar`

Import it where you need it and don't forget to add it to your `components` object.
## How to use it:

You need to provide statistics, polycolor, radar and scale data to use the v-radar component.

> Example:

> [![example](https://www.nicolas-gomes.com/assets/images/posts/2017-09-24/radar.png)](https://www.nicolas-gomes.com/2017/09/24/vue-radar.html)


#### template:
```html
<v-radar
    :stats="statistics"
    :polycolor="polycolor"
    :radar="radar"
    :scale="scale">
</v-radar>
```

#### Minimum script required:
```javascript
import Radar from 'vue-radar'

new Vue({
    components: {
        'radar': Radar
    },
    data: {
        radar: {},                          // empty object is mandatory
        scale: {},                          // empty object is mandatory
        stats: [                            // at least 3 stats are required here
            {
                name: 'stat1',              // string
                value: 12,                  // int
            },
            {
                name: 'stat2',  
                value: 77,      
            },
            {
                name: 'stat3',
                value: 44,
            },
        ],
        polycolor: 'rgba(250, 100, 50, .5)' // any css format is usable (hexa, rgb, rgba...)
    },
})
```

### Full data available:

```javascript
import Radar from 'vue-radar'

new Vue({
    components: {
        'radar': Radar
    },
    data: {
        radar: {
            size: '400',                                // pixel unit
            viewbox: '1000',                            // unit used inside the svg (here 400px = 1000 unités)
            radius: '350',                              // same unit than above (diamètre = 900), keep the radius < (viewbox / 2)
            structure: {
                external: {                             // external stroke of the structure's polygon
                    strokeColor: 'rgba(0, 0, 0, 1)',    // color (any css format is usable (hexa, rgb, rgba...))
                    strokeWidth: '4',                   // pixel unit
                },
                internals: {                            // internals stroke of the structure's polygon (one every 10%)
                    strokeColor: 'rgba(0, 0, 0, .3)',   // color (any css format is usable (hexa, rgb, rgba...))
                    strokeWidth: '1',                   // pixel unit
                },
                average: {                              // average polygon (placed at 50%)
                    strokeColor: 'rgba(0, 0, 0, 1)',    // stroke color (any css format is usable (hexa, rgb, rgba...))
                    strokeWidth: '2',                   // pixel unit
                    fillColor: 'rgba(0, 0, 0, .5)',     // polygon color (any css format is usable (hexa, rgb, rgba...))
                },
            },
            lines: {                                    // lines from center to summits
                strokeColor: 'rgba(0, 0, 0, .3)',       // color (any css format is usable (hexa, rgb, rgba...))
                strokeWidth: '1',                       // pixel unit
            }
        },
        scale: {                                        // scales of corresponding statistic
            stat1: 24,                                  // key must be equal to the corresponding statistic, lowercased and without accents
            stat2: 100,
            stat3: 100,
        },
        stats: [
            {
                name: 'stat1',                          // string
                value: 12,                              // int
                shortName: 'A',                         // The two first letters are used to be display next to their corresponding summits
            },
            {
                name: 'stat2',
                value: 77,
                shortName: 'Super stat'
            },
            {
                name: 'stat3',
                value: 44,
                shortName: 'st'
            },
        ],
        polycolor: 'rgba(250, 100, 50, .5)'             // color (any css format is usable (hexa, rgb, rgba...))
    },
})
```
