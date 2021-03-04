<template>
    <div>                                        
        <div class="row">        
            <b-container fluid class="table-container">
                <table class="table">
                    <thead>
                        <tr>
                            <th @click="sort('Severity')">Severity</th>
                            <th @click="sort('VulnerabilityID')">Cve</th>
                            <th @click="sort('PkgName')">Package</th>
                            <th @click="sort('InstalledVersion')">Version</th>                            
                        </tr>
                    </thead>
                    <tr :id="item.VulnerabilityID" v-for="item in filteredItems" :key="item.id" v-b-hover="handleHover" v-bind:class="{ active: hovered == item.VulnerabilityID }">
                        <td>
                            <cve-level :showIcon=true :severity="item.Severity"></cve-level>
                        </td>
                        <td>                            
                            {{ item.VulnerabilityID }}
                        </td>
                        <td>{{ item.PkgName }}</td> 
                        <td>{{ item.InstalledVersion }}</td>                        
                    </tr>        
                </table>        
            </b-container>
        </div>
    </div>
</template>

<script>    
    import CveLevel from '@/components/cveLevel.vue';

    export default {
        name: "CveList",
        components: {            
            CveLevel
        },
        props: {            
            id: String,   
            cveFilter: String,
            appFilter: String,
            details: Object,
        },        
        data: () => ({
            hovered: null,            
            currentSort:'Severity',
            currentSortDir:'asc'
        }),
        mounted () {
        },
        methods: {            
            handleHover(hovered, item) {          
                let hoveredCve = null;                      
                if (hovered && item) {
                    hoveredCve = this.details.Vulnerabilities.find( cve => {
                        return item.path[0].id == cve.VulnerabilityID;
                    });                    
                }
                if (hoveredCve) {
                    this.hovered = hoveredCve.VulnerabilityID;
                    this.$msgbus.$emit('hovered-cve', hoveredCve );                
                }                
            },
            sort(s) {                
                if(s === this.currentSort) {
                this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
                }
                this.currentSort = s;
            }
        },
        computed: {
            filteredItems() {                                
                if (!this.details.Vulnerabilities) return [];
                return this.details.Vulnerabilities
                .filter(cve => {
                    return cve.VulnerabilityID.toLowerCase().indexOf(this.cveFilter.toLowerCase()) > -1
                })
                .filter(cve => {
                    return cve.PkgName.toLowerCase().indexOf(this.appFilter.toLowerCase()) > -1
                })
                .sort((a,b) => {
                    let modifier = 1;
                    if(this.currentSortDir === 'desc') modifier = -1;
                    if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
                    if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
                    return 0;
                });
            }            
        }

    };
</script>

<style scoped>

.table-container {
     height: calc(100vh - 450px);
     overflow:scroll;
}

.active {
    background-color: lightgray;
}

.green {
    color:#2eaf34;
}

.blue {
    color:#425cb3;
}

.red {
    color: #FF6245;
}

.yellow {
    color: #FFC107;
}

</style>

