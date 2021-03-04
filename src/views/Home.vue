<template>
  <div class="container large-container">
    <div class="text-center">
      <img class="d-block mx-auto mb-4" src="https://stereum.net/wp-content/uploads/2020/11/icon__eth.png" alt="" width="72" height="72">
      <h2>stereum.net CVE List</h2>
      <p class="lead">Shows you the security information of all the stereum managed images.</p>
    </div>
    <div class="row">
      <div class="col-md-4 order-md-2 mb-4">
        <h4 class="d-flex justify-content-between align-items-center mb-3">
          <span class="text-muted">Details {{ hoveredItem ? `${hoveredItem.image}:${hoveredItem.tag}` : ''}} </span>          
        </h4>
        <ul class="list-group mb-3" v-if="hoveredItem">          
          <li class="list-group-item d-flex justify-content-between lh-condensed">                                    
            <small class="text-muted">Last scanned {{hoveredItem.scanned}}</small>
          </li>          
          <li class="list-group-item d-flex justify-content-between lh-condensed">
            <div>                                            
                <span class="text-muted">Vulnerabilities with known fixes</span>                
                &nbsp;<span class="badge badge-secondary badge-pill bg-green" v-bind:class="{'bg-red': hoveredItem.fixed && hoveredItem.fixed.critical > 0 }">{{ hoveredItem.fixed && hoveredItem.fixed.critical }}</span>
                &nbsp;<span class="badge badge-secondary badge-pill bg-green" v-bind:class="{'bg-yellow': hoveredItem.fixed && hoveredItem.fixed.high > 0 }">{{ hoveredItem.fixed && hoveredItem.fixed.high }}</span>
                &nbsp;<span class="badge badge-secondary badge-pill bg-green" v-bind:class="{'bg-blue': hoveredItem.fixed && hoveredItem.fixed.low > 0 }">{{ hoveredItem.fixed && hoveredItem.fixed.low }}</span>
                <br/>
                <small class="text-muted">This means that these Vulnerabilities can be fixed by updating OS packages or libraries</small>                                  
            </div>
          </li>
          <li class="list-group-item d-flex justify-content-between lh-condensed">
            <div>                            
                <span class="text-muted">Vulnerabilities with no fixes</span>
                &nbsp;<span class="badge badge-secondary badge-pill" v-bind:class="{'bg-red': hoveredItem.unfixed && hoveredItem.unfixed.critical > 0 }">{{ hoveredItem.unfixed && hoveredItem.unfixed.critical }}</span>
                &nbsp;<span class="badge badge-secondary badge-pill" v-bind:class="{'bg-yellow': hoveredItem.unfixed && hoveredItem.unfixed.high > 0 }">{{ hoveredItem.unfixed && hoveredItem.unfixed.high }}</span>
                &nbsp;<span class="badge badge-secondary badge-pill" v-bind:class="{'bg-blue': hoveredItem.unfixed && hoveredItem.unfixed.low > 0 }">{{ hoveredItem.unfixed && hoveredItem.unfixed.low }}</span>
                <br/>
                <small class="text-muted">This means you can't fix these vulnerabilities even if all OS packages and libraries are on the latest release</small>                
            </div>
          </li>          
        </ul>        
      </div>
      <div class="col-md-8 order-md-1">
        <div class="needs-validation" novalidate>
          <div class="row">
            <div class="col-md-6 mb-3">
              <input type="text" class="form-control" id="image" v-model="imageFilter" placeholder="Filter by repository">
            </div>
            <div class="col-md-6 mb-3">
              <input type="text" class="form-control" id="tag" v-model="tagFilter" placeholder="Filter by tag">
            </div>            
          </div>

          <div class="mb-3">
            <ImageReportList :images="images" :imageFilter="imageFilter" :tagFilter="tagFilter"></ImageReportList>
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
import ImageReportList from "@/components/ImageReportList.vue";

export default {
  name: "Home",
  components: {
    ImageReportList,
  },
  data: () => ({
    images: [],
    imageFilter: "",
    tagFilter: "",
    unfixedFilter: "",
    hoveredItem: {},
  }),
  computed: {        
  },
  mounted() {
    this.$msgbus.$on("hovered-image", (data) => {
      this.hoveredItem = data;
    });

    axios
      .get("/data/imagelist.json")
      .then((response) => (this.images = response.data));
  },
};
</script>

<style scoped>
.large-container {
  max-width: 95%;
}

.green {
  color: #2eaf34;
}

.bg-green {
  background-color: #2eaf34;
}

.blue {
  color: #425cb3;
}

.bg-blue {
  background-color: #425cb3;
}

.red {
  color: #ff6245;
}

.bg-red {
  background-color: #ff6245;
}

.yellow {
  color: #ffc107;
}

.bg-yellow {
  background-color: #ffc107;
}
</style>
