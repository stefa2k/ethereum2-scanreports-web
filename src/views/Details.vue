<template>
<div class="container large-container">
    <div class="text-center">
      <img class="d-block mx-auto mb-4" src="https://stereum.net/wp-content/uploads/2020/11/icon__eth.png" alt="" width="72" height="72">
      <h2>{{ imagedetails.image }}:{{ imagedetails.tag || 'latest' }}</h2>
      <p class="lead">Shows you the known CVEs of this image.</p>
    </div>
    <div class="row">
      <div class="col-md-4 order-md-2 mb-4">
        <h4 class="d-flex justify-content-between align-items-center mb-3">
          <span class="text-muted">Details</span>
          <span class="badge badge-secondary badge-pill"></span>
        </h4>
        <ul class="list-group mb-3" v-if="hoveredItem">
          <li class="list-group-item d-flex justify-content-between lh-condensed">            
              <h6>                  
                <span>&nbsp;ISSUE&nbsp;{{ hoveredItem.VulnerabilityID }}</span>
              </h6>                          
          </li>
          <li class="list-group-item d-flex justify-content-between lh-condensed">            
              <span>Fix available: {{ !!hoveredItem.FixedVersion }}</span>
          </li>
          <li class="list-group-item d-flex justify-content-between lh-condensed">            
              <span>{{ hoveredItem.Description }}</span>
          </li>
        </ul>

        <div class="card p-2" v-if="hoveredItem">
          <small>Found {{ hoveredItem.PublishedDate }} for {{ hoveredItem.SeveritySource }} </small>
        </div>
      </div>
      <div class="col-md-8 order-md-1">
        <div class="needs-validation" novalidate>
          <div class="row">
            <div class="col-md-6 mb-3">
              <input type="text" class="form-control" id="image" v-model="cveFilter" placeholder="Filter by cve">
            </div>
            <div class="col-md-6 mb-3">
              <input type="text" class="form-control" id="tag" v-model="appFilter" placeholder="Filter by app">
            </div>
          </div>

          <div class="mb-3">
            <cve-list :details="imagedetails" :cveFilter="cveFilter" :appFilter="appFilter" ></cve-list>
          </div>
          <hr class="mb-4">
      </div>
    </div>

    <footer class="my-5 pt-5 text-muted text-center text-small">
    </footer>
    </div>
  </div>
</template>
<script>    
    import axios from "axios";    
    import CveList from '@/components/cveList.vue';        

    export default {      
      name: "Details",
      components: {
        CveList        
      },         
      props: {
      },        
      data: () => ({
          imagedetails: {},
          hoveredItem: {},
          cveFilter: '',
          appFilter: '',
          outdatedFilter: ''
      }),
        mounted () {    
          this.$msgbus.$on('hovered-cve', (data) => {
          this.hoveredItem = data;          
        });        
        
        let detailFilename = `/data/${this.$route.params.id}.json`
        axios
          .get(detailFilename)
          .then(response => {
            response.data.Vulnerabilities = response.data.Vulnerabilities
              .map(vul => {
                vul.id = `${vul.VulnerabilityID}-${vul.PkgName}`;
                return vul;
              });
            this.imagedetails = response.data;            
          })
      },
      methods: {
          expand(id) {                                                                
              this.expanded[id] = !this.expanded[id];                                
          },
          handleHover(hovered, item) {          
              let hoveredImage = null;
              if (hovered && item) {
                  hoveredImage = this.images.find( image => {
                      return item.path[0].id == image.id;
                  });                    
              }                
              this.$msgbus.$emit('hovered', { type: 'image', data: hoveredImage });                
          },
          isCollapsed(id) {
              return this.expanded[id] == true;
          }
      },
      computed: {           
      }
  }
</script>

<style scoped>
.large-container {
    max-width: 95%;
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

