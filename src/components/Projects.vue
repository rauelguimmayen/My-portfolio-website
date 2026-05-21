<template>
<section class="pb-5" id="projects">

    <div class="text-center mb-5 fade-up">
      <p class="section-label">Work</p>
      <h2 class="section-title">My Projects</h2>
    </div>

    <div class="row row-cols-1 row-cols-md-3 g-4 justify-content-center">
      <div class="col" v-for="project in projects" :key="project.id">
        <ProjectCard :project="project" />
      </div>
    </div>

  </section>>
</template>
<script setup>
	import { computed } from 'vue'
	import ProjectCard from './ProjectCard.vue';
	import projects from '../data/projects.json'

	const chunkSize = 3;

	const chunkedProjects = computed (() => {
		const chunks = [];

		for(let i = 0; i < projects.length; i += chunkSize){
			chunks.push(projects.slice(i, i + chunkSize));
		}
		return chunks;
	})
</script>