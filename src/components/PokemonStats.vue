<template>

        <canvas id="statusChart" ref="statusChart"></canvas>

</template>

<script>
import { Chart, RadarController} from 'chart.js/auto';

export default {
    data() {
        return {
            statusChart: null,
        }
    },
    props: {
        pokemonStats: {
            type: Object,
        }
    },
    methods: {
        drawChart(pokemonStats) {
            
            if (this.statusChart) {
                this.statusChart.destroy(); // Destroy the existing chart instance if it exists
            }

            let statusChart = document.getElementById('statusChart');

            const data = {
                labels: (function() {
                    // Some logic here
                    return pokemonStats.map(status => status.stat.name.charAt(0).toUpperCase() + status.stat.name.slice(1));
                })(),
                datasets: [{
                    label: '',
                    data: (function() {
                        // Some logic here
                        return pokemonStats.map(status => status.base_stat);
                    })(),
                    fill: true,
                    backgroundColor: 'rgba(255, 99, 132, 0.2)',
                    borderColor: 'rgb(255, 99, 132)',
                    pointBackgroundColor: 'rgb(255, 99, 132)',
                    pointBorderColor: '#fff',
                    pointHoverBackgroundColor: '#fff',
                    pointHoverBorderColor: 'rgb(255, 99, 132)'
                },
                {data: [0], label: ''}
                ]
            };

            const config = {
                type: 'radar',
                data: data,
                options: {
                    plugins: {
                        legend: {
                            display: false
                    }

                },
                tooltips: {
                    callbacks: {
                    label: function(tooltipItem) {
                            return tooltipItem.yLabel;
                    }
                    }
                },
                elements: {
                    line: {
                    borderWidth: 3
                    }
                }
                },
            };

            this.statusChart = new Chart(statusChart, config);
            console.log('chart has been drawn')
            this.$emit('chartDone')

            }

    },
    watch: {
        pokemonStats() {
        
        this.drawChart(this.pokemonStats)
        
    }
    }
}
</script>