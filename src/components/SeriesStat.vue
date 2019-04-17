<template>
    <div class="main">
        <div class="vuest">
          <v-select  v-model="types" :options="setTypes" label="name">
            </v-select>
        <VueHotelDatepicker v-if="boolDate"  v-model="dates" minDate="2018-10-10" @update="getDates"/>
            <button v-if="boolDate" @click="getValueAll" class="btn btn-default">Общие просмотры</button>
        </div>
        <v-select v-if='boolData'  v-model="id" :options="setData" label="name">
        </v-select>
         <v-select v-if="boolSeries"  v-model="idSeries" :options="valueSeries" label="name">
        </v-select>
        <div  ref="chart" class="char"></div>
    </div>
</template>

<script>
    import * as am4core from "@amcharts/amcharts4/core";
    import * as am4charts from "@amcharts/amcharts4/charts";
    import am4themes_animated from "@amcharts/amcharts4/themes/animated";
    am4core.useTheme(am4themes_animated);
    import VueHotelDatepicker from '@northwalker/vue-hotel-datepicker'
    import vSelect from 'vue-select'
    export default {
        name: "SeriasStatistic",
        data(){
            return {
                setData:[],id: '',
                boolData:false,
                boolDate:false,
                boolSeries:false,
                //boolGraph:false,
                valueSeries : [],
                idSeries :'',
                dates:{},
                status1:false,
                setTypes:['manga','anime'],
                types:''

            }

        },
        components: {
            vSelect,VueHotelDatepicker
        },
        methods: {
            getSeries() {
                fetch('https://risens.team/risensteam/api/'+this.types+'.php?id=' + this.id.id)
                    .then((response) => {
                        if (response.ok) {
                            return response.json();
                        }

                        throw new Error('Network response was not ok');
                    })
                    .then((json) => {
                        this.valueSeries = json;
                        this.boolSeries=true;
                    })
                    .catch((error) => {
                        console.log(error);
                    });
            },
            getValueGraph() {
                fetch('https://risens.team/risensteam/api/stat_anime.php?id=' + this.idSeries.id + '&time=' + this.dates.start+'&timeend=' + this.dates.end)
                    .then((response) => {
                        if (response.ok) {
                            return response.json();
                        }

                        throw new Error('Network response was not ok');
                    })
                    .then((json) => {
                        this.getGraph(json);
                        console.log(this.types);
                    })
                    .catch((error) => {
                        console.log(error);
                    });
            },
            getValueAll(){
                fetch('https://risens.team/risensteam/api/stat_final.php?time=' + this.dates.start+'&timeend=' + this.dates.end)
                    .then((response) => {
                        if (response.ok) {
                            return response.json();
                        }

                        throw new Error('Network response was not ok');
                    })
                    .then((json) => {
                        this.valueSeries = json;

                        this.getGraph(json);

                    })
                    .catch((error) => {
                        console.log(error);
                    });
            },
            getDates (dates) {
                this.dates=dates;
                this.dates.start+="%200";
                this.dates.end+="%200";
                this.dates.start=this.dates.start.split("/").join("-");
                this.dates.end=this.dates.end.split("/").join("-");
                console.log(this.dates);
                this.boolData=true;
            },
            getGraph(json) {
              //  this.boolGraph=true;
                var chart = am4core.create(this.$refs.chart, am4charts.XYChart);
                chart.paddingRight = 20;

                // Add data
                chart.data = json;

                // Create axes
                var categoryAxis = chart.xAxes.push(new am4charts.CategoryAxis());
                categoryAxis.dataFields.category = "my_date";
                categoryAxis.renderer.minGridDistance = 50;
                categoryAxis.renderer.grid.template.location = 0.5;
                categoryAxis.startLocation = 0.5;
                categoryAxis.endLocation = 0.5;

                // Pre zoom
                chart.events.on("datavalidated", function () {
                    categoryAxis.zoomToIndexes(Math.round(chart.data.length * 0.4), Math.round(chart.data.length * 0.55));
                });

                // Create value axis
                var valueAxis = chart.yAxes.push(new am4charts.ValueAxis());
                valueAxis.baseValue = 0;

                // Create series
                var series = chart.series.push(new am4charts.LineSeries());
                series.dataFields.valueY = "value";
                series.dataFields.categoryX = "my_date";
                series.strokeWidth = 2;
                series.tensionX = 0.77;

                var range = valueAxis.createSeriesRange(series);
                range.value = 0;
                range.endValue = 1000;
                range.contents.stroke = am4core.color("#FF0000");
                range.contents.fill = range.contents.stroke;

                // Add scrollbar
                var scrollbarX = new am4charts.XYChartScrollbar();
                scrollbarX.series.push(series);
                chart.scrollbarX = scrollbarX;

                chart.cursor = new am4charts.XYCursor();


            },
            getStart() {
                fetch('https://risens.team/risensteam/api/catalog.php?type='+this.types)
                    .then((response) =>
                    {

                        if(response.ok)
                        {
                            return response.json();
                        }

                        throw new Error('Network response was not ok');
                    })
                    .then((json) =>{
                        this.setData=json;
                        this.boolDate=true;
                    })
                    .catch((error) => {
                        console.log(error);
                    });
            }
        },
        watch:{
            id:function () {
                this.getSeries()
            },
            idSeries(){
                this.getValueGraph();
            },
            types:function()
            { this.boolData=false;
              this.boolDate=false;
              this.boolSeries=false;
            //  this.boolGraph=false;
              this.getStart();

            }
        }

    }



</script>

<style>
    .char{
    height: 500px;
    }
</style>
