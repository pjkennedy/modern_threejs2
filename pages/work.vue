<template>

  <div class="bg-black h-screen px-6 py-12">
    <h2 class="text-white font-exo text-5xl opacity-0"
      ref="title" style="transform: translateY(30px);">MY WORK</h2>
    <div class="flex space-x-4">
        <div ref="projects" v-for="project in projects" class="w-full opacity-0">
        <nuxt-link to="/">
            <img :src="project.image.url"
            style="height: 70vh; transform: translateY(30px);" class="object-cover"
            :alt="project.alt" />
            <p ref="caption" class="text-white text-sm opacity-0
              font-space-mono" style="transform: translateY(30px)">
              {{project.title}}</p>
        </nuxt-link>
    </div>

  </div>
  </div>
</template>

<script>
import gsap from 'gsap'

import internetImg from '~/assets/screen_blues.jpg'
import web_devImg from '~/assets/screen_purples.jpg'
import radio_towerImg from '~/assets/radio_tower.jpg'
import enterpriseImg from '~/assets/enterprise.jpg'
import programmingImg from '~/assets/coding.jpg'

export default {
  data() {
    return {
      projects: [
        {
          image: {
            url: internetImg
          },
          title: 'Networking',
          alt: 'Networking'
        },
        {
          image: {
            url: web_devImg
          },
          title: 'Web Development',
          alt: 'Web Development'
        },
        {
          image: {
            url: enterpriseImg
          },
          title: 'Enterprise',
          alt: 'Enterprise'
        },

        {
          image: {
            url: radio_towerImg
          },
          title: 'Communications',
          alt: "Communications"
        },
        {
          image: {
            url: programmingImg
          },
          title: 'Coding',
          alt: "Programming"
        },

      ]
    }
  },
  mounted() {
    gsap.to(this.$refs.title, {
      opacity: 1,
      duration: 1.5,
      y: 0,
      ease: 'expo'
    })
    gsap.to(this.$refs.projects, {
      opacity: 1,
      duration: 1.5,
      stagger: 0.1,
      x: 0,
      ease: 'expo',
      delay: 0.2
    })
    gsap.to(this.$refs.caption, {
      opacity: 1,
      duration: 2
    })


  }
}
</script>
