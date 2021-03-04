<template>    
    <div class="col-md-12">                                
        <table class="table">
            <thead>
                <tr>
                    <th @click="sort('image')">Image</th>
                    <th @click="sort('tag')">Tag</th>
                    <th>Critical</th>
                    <th>High</th>
                    <th>Low</th>                        
                </tr>
            </thead>
            <tr @click="handleClick(item)" v-bind:class="{ active: hovered == item.id }" :id="item.id" v-b-hover="handleHover" v-for="item in filteredItems" :key="item.id">                
                <td>
                    <div v-bind:class="{ active: hovered == item.id }">
                        <strong>{{ item.image }}</strong>                                                        
                    </div>                    
                </td>
                <td>                    
                    <strong>{{ item.tag }}</strong>
                </td>
                <td>
                    <i v-bind:class="{'fa-exclamation-triangle red': item.fixed.crititcal > 0 }" class="fa fa-check-circle green"></i>&nbsp;{{ item.fixed.critical }}/{{ item.unfixed.critical }}
                </td>
                <td>
                    <i v-bind:class="{'fa-exclamation-triangle yellow': item.fixed.high > 0 }" class="fa fa-check-circle green"></i>&nbsp;{{ item.fixed.high }}/{{ item.unfixed.high }}
                </td>
                <td>
                    <i v-bind:class="{'fa-exclamation-triangle blue': item.fixed.low > 0 }" class="fa fa-check-circle green"></i>&nbsp;{{ item.fixed.low }}/{{ item.unfixed.low }}
                </td>
            </tr>        
        </table>
    </div>    
</template>

<script>
    export default {
        components: { },
        name: "ImageReportList",
        props: {            
            imageFilter: String,
            tagFilter: String,
            images: Array,            
        },        
        data: () => ({    
            hovered: null,
            currentSort:'name',
            currentSortDir:'asc'
        }),
        mounted () {                        
        },
        methods: {            
            handleHover(hovered, item) {                          
                let hoveredImage = null;
                if (hovered && item) {
                    hoveredImage = this.images.find( image => {
                        return item.path[0].id == image.id;
                    });
                }                      
                if (hoveredImage) {
                    this.hovered = hoveredImage.id;
                    this.$msgbus.$emit('hovered-image', hoveredImage );                
                }   
            },
            handleClick(item) {     
                let id = item.id;
                this.$router.push({ path: `/images/${id}` });
            },
            isHovered(item) {
                return this.hovered == item.id;
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
                return this.images
                .filter(item => {
                    return item.image.toLowerCase().indexOf(this.imageFilter.toLowerCase()) > -1
                })
                .filter(item => {
                    return item.image.toLowerCase().indexOf(this.tagFilter.toLowerCase()) > -1
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

