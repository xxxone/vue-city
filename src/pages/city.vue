<template>
	<div>
		<selectCity></selectCity>
	</div>

</template>
<script>
	import selectCity from '@/components/selectCity.vue'
	export default {
	  name: 'city',
	  data () {
	    return {
	    }
	  },
		components: {
		    selectCity
		},
	  method:{
	    enter:function(){
	      this.$router.push({name: 'city'})
	    }
	  }
	}
</script>