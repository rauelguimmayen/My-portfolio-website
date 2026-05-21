<template>
	<div>
	    <div class="project-card fade-up">
	      <div class="project-thumb">
	      	<img class="project-img" :src="project.imageURL" alt="...">
	      </div>
	      <div class="project-body">
	        <p class="project-name">{{project.title}}</p>
	        <p class="project-desc">{{project.description}}</p>
	      </div>
	    </div>
	 </div>   
</template>
<script setup>
	import { defineProps} from 'vue'
	
	defineProps({
		project: Object
		
	})
</script>