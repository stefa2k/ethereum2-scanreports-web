<template>
    <span>                                                
        <i v-if="showIcon" class="fa fa-exclamation-triangle" :class="getClass"></i>
        &nbsp;{{ severity }}
    </span>
</template>

<script>
    export default {
        name: "CveLevel",
        props: {            
            severity: String,
            showIcon: Boolean,
        },
        computed: {
            getClass() {
                if (this.severity == 'LOW') return 'LOW';
                if (this.severity == 'HIGH') return 'HIGH';
                if (this.severity == 'CRITICAL') return 'CRITICAL';
                return 'green'
            }
        }
    };
</script>

<style scoped>

.green {
    color:#2eaf34;
}

.LOW {
    color:#425cb3;
}

.CRITICAL {
    color: #FF6245;
}

.HIGH {
    color: #FFC107;
}

</style>

