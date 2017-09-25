<template>
  <div class="v-radar">
    <svg :width="svgSize" :height="svgSize" :viewBox="svgViewBox">
      <polygon
              :points="buildStructure(1)"
              :fill="transparent"
              :stroke="externalStrokeColor"
              :stroke-width="externalStrokeWidth"
      ></polygon>
      <polygon
              :points="buildStructure(n/10)"
              :fill="transparent"
              :stroke="internalsStrokeColor"
              :stroke-width="internalsStrokeWidth"
              v-for="n in 9"
      ></polygon>
      <polygon
              :points="buildStructure(0.5)"
              :fill="averageFillColor"
              :stroke="averageStrokeColor"
              :stroke-width="averageStrokeWidth"
      ></polygon>
      <polygon :points="buildPoints" :fill="polyFillColor"></polygon>
      <line
              :x1="originPoint"
              :y1="originPoint"
              :x2="xLineCoordinate(index)"
              :y2="yLineCoordinate(index)"
              :stroke="linesStrokeColor"
              :stroke-width="linesStrokeWidth"
              v-for="index in statsLength"
      >
      </line>
      <g class="label">
        <text
                :x="xTextCoordinate(index)"
                :y="yTextCoordinate(index)"
                font-family="Verdana"
                font-size="50"
                v-for="index in statsLength"
                v-text="shortNames[index]"
        >
        </text>
      </g>
    </svg>
  </div>
</template>
<script>
    export default {
        name: 'radar',
        props: {
            radar: {
                type: Object,
                required: false,
            },
            stats: Array,
            polycolor: String,
            scale: Object,
        },
        data() {
            return {
                transparent: 'rgba(0, 0, 0, 0)'
            }
        },
        methods: {
            buildStructure(percentage) {
                let points = '';

                for (let count = 0; count < this.statsLength; count++) {
                    let newAngle = calculateNewAngle(count, this.baseAngle);
                    let angleMultiplier = percentage * this.radius;
                    let x = calculateXCoordinate(newAngle, angleMultiplier, this.originPoint);
                    let y = calculateYCoordinate(newAngle, angleMultiplier, this.originPoint);
                    points += Math.round(x) + ',' + Math.round(y) + ' ';
                }

                return points;
            },
            xLineCoordinate(index) {
                let newAngle = calculateNewAngle((index - 1), this.baseAngle);

                return calculateXCoordinate(newAngle, this.radius, this.originPoint);
            },
            yLineCoordinate(index) {
                let newAngle = calculateNewAngle((index - 1), this.baseAngle);

                return calculateYCoordinate(newAngle, this.radius, this.originPoint);
            },
            xTextCoordinate(index) {
                let newAngle = calculateNewAngle((index - 1), this.baseAngle);

                return calculateXCoordinate(newAngle, parseFloat(this.radius) + 60, this.originPoint) - 20;
            },
            yTextCoordinate(index) {
                let newAngle = calculateNewAngle((index - 1), this.baseAngle);

                return calculateYCoordinate(newAngle, parseFloat(this.radius) + 60, this.originPoint) + 20;
            },
        },
        computed: {
            svgSize() {
                return setProperty(this.radar, 'size', '400') + 'px';
            },
            viewbox() {
                return setProperty(this.radar, 'viewbox', '1000');
            },
            svgViewBox() {
                return '0 0 ' + this.viewbox + ' ' + this.viewbox;
            },
            statsLength() {
                return objectLength(this.stats);
            },
            baseAngle() {
                return calculateBaseAngle(this.statsLength);
            },
            radius() {
                return setProperty(this.radar, 'radius', '350');
            },
            originPoint() {
                return this.viewbox / 2;
            },
            externalStrokeColor() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'structure')) {
                        if (objectHasProperty(this.radar.structure, 'external')) {
                            return setProperty(this.radar.structure.external, 'strokeColor', '#222222');
                        }
                    }
                }

                return '#222222';
            },
            externalStrokeWidth() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'structure')) {
                        if (objectHasProperty(this.radar.structure, 'external')) {
                            return setProperty(this.radar.structure.external, 'strokeWidth', '4') + 'px';
                        }
                    }
                }

                return '4px';
            },
            internalsStrokeColor() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'structure')) {
                        if (objectHasProperty(this.radar.structure, 'internals')) {
                            return setProperty(this.radar.structure.internals, 'strokeColor', '#777777');
                        }
                    }
                }

                return '#777777';
            },
            internalsStrokeWidth() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'structure')) {
                        if (objectHasProperty(this.radar.structure, 'internals')) {
                            return setProperty(this.radar.structure.internals, 'strokeWidth', '2') + 'px';
                        }
                    }
                }

                return '2px';
            },
            linesStrokeColor() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'lines')) {
                        return setProperty(this.radar.lines, 'strokeColor', '#000000');
                    }
                }

                return '#000';
            },
            linesStrokeWidth() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'lines')) {
                        return setProperty(this.radar.lines, 'strokeWidth', '2') + 'px';
                    }
                }

                return '2px';
            },
            averageStrokeColor() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'structure')) {
                        if (objectHasProperty(this.radar.structure, 'average')) {
                            return setProperty(this.radar.structure.average, 'strokeColor', '#777777');
                        }
                    }
                }

                return '#777';
            },
            averageStrokeWidth() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'structure')) {
                        if (objectHasProperty(this.radar.structure, 'average')) {
                            return setProperty(this.radar.structure.average, 'strokeWidth', '2') + 'px';
                        }
                    }
                }

                return '2px';
            },
            averageFillColor() {
                if (objectIsDefined(this.radar)) {
                    if (objectHasProperty(this.radar, 'structure')) {
                        if (objectHasProperty(this.radar.structure, 'average')) {
                            return setProperty(this.radar.structure.average, 'fillColor', 'rgba(0, 0, 0, .2)');
                        }
                    }
                }

                return 'rgba(0, 0, 0, .2)';
            },
            buildPoints() {
                let points = '';
                let divider = 1;

                for (let i = 0; i < this.statsLength; i++) {
                    let name = this.stats[i].name;
                    let value = this.stats[i].value;
                    if (this.scale.hasOwnProperty(removeAccents(name.toLowerCase()))) {
                        divider = this.scale[removeAccents(name.toLowerCase())];
                    } else {
                        divider = 100;
                    }
                    let newAngle = calculateNewAngle(i, this.baseAngle);
                    let angleMultiplier = value / divider * this.radius;
                    let x = calculateXCoordinate(newAngle, angleMultiplier, this.originPoint);
                    let y = calculateYCoordinate(newAngle, angleMultiplier, this.originPoint);
                    points += Math.round(x) + ',' + Math.round(y) + ' ';
                }

                return points;
            },
            shortNames() {
                let shortNames = [0];
                for (let i = 0; i < this.statsLength; i++) {
                    let stat = this.stats[i];
                    if (objectHasProperty(stat, 'shortName')) {
                        shortNames.push(setProperty(stat, 'shortName', stat.name).slice(0, 2));
                    } else {
                        shortNames.push(stat.name.slice(0, 2));
                    }
                }

                return shortNames;
            },
            polyFillColor() {
                if (this.polycolor === '') {
                    return 'rgba(200, 50, 150, .7)';
                } else {
                    return this.polycolor;
                }
            }
        }
    }

    function objectIsDefined(object) {
        return (typeof object !== 'undefined');
    }

    function objectHasProperty(object, property) {
        if (typeof object !== 'undefined') {
            if (object.hasOwnProperty(property)) {
                return true;
            }
        }

        return false;
    }

    function setProperty(object, propertyToTest, defaultValue) {
        if (objectHasProperty(object, propertyToTest)) {
            if (typeof object[propertyToTest] !== 'undefined') {
                return object[propertyToTest];
            }
        }

        return defaultValue;
    }

    // @return the obj length
    function objectLength(obj) {
        let i;
        let length = 0;
        for (i in obj) {
            length++;
        }

        return length;
    }

    // @return the angle of one part based on total
    function calculateBaseAngle(total) {
        return Math.PI * 2 / total;
    }

    // @return the angle of the index
    function calculateNewAngle(index, baseAngle) {
        return (Math.PI / 2) + Math.PI + (baseAngle * index);
    }

    function calculateXCoordinate(angle, angleMultiplier, origin) {
        return origin + Math.cos(angle) * angleMultiplier;
    }

    function calculateYCoordinate(angle, angleMultiplier, origin) {
        return (origin + Math.sin(angle) * angleMultiplier);
    }

    function removeAccents(str) {
        let accents = 'ÀÁÂÃÄÅàáâãäåÒÓÔÕÕÖØòóôõöøÈÉÊËèéêëðÇçÐÌÍÎÏìíîïÙÚÛÜùúûüÑñŠšŸÿýŽž';
        let accentsOut = "AAAAAAaaaaaaOOOOOOOooooooEEEEeeeeeCcDIIIIiiiiUUUUuuuuNnSsYyyZz";
        str = str.split('');
        let strLen = str.length;
        let i, x;
        for (i = 0; i < strLen; i++) {
            if ((x = accents.indexOf(str[i])) !== -1) {
                str[i] = accentsOut[x];
            }
        }

        return str.join('');
    }
</script>
<style>

</style>
