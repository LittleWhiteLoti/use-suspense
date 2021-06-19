<template>
  <Suspense>
    <template #default>
      <!-- <featured-characters
       :featuredItems="featuredCharacters"
       :loadingData="charactersLoading"
       :title="'Featured Characters'"
      ></featured-characters> -->
      <featured-characters>
      </featured-characters>
    </template>
    <template #fallback>
      This fall back is being overridden by the LoadingError component because of suspensible false
    </template>
  </Suspense>
  <featured-comics
    :featuredComics="latestComics"
    :loadingComics="comicsLoading"
  ></featured-comics>
</template>

<script>

//import FeaturedCharacters from "./MyFeaturedCharacters.vue"

import { watch, computed, onMounted, defineAsyncComponent } from 'vue'
import { useStore } from "vuex"
import Loading from './Loading.vue'
import LoadingError from './LoadingError.vue'
import FeaturedComics from "./MyFeaturedComics.vue"

const FeaturedCharacters = defineAsyncComponent({
  /* Component that is meant to be dynamically loaded */
  loader: () => import("./MyFeaturedCharacters.vue"),
  /* Place holder component while the component is waiting to be loaded into the DOM */
  loadingComponent: Loading,
  /* Delay in miliseconds that will be used before the results are displayed */
  /* (Optional) */
  // delay: 200,
  /* Place holder component if the component fails to load */
  /* (Optional) but it is a good idea if you don't have a template #fallback on the suspense*/
  errorComponent: LoadingError,
  /* Timeout in milliseconds is used to say how long the component has to load before it automatically fails  */
  /* (Optional) but it is a good idea because the user needs to know something is happening */
  //timeout: 3000,
  /* Ignore the parent suspense boundary if the current component MyCurrentPage.vue is surrounded by a suspend component */
  /* (Optional) only needed if you plan on overriding the template #fallback */
  suspensible: false
})

export default {
  components: {
    FeaturedCharacters,
    FeaturedComics
  },
  setup() {
    const store = useStore()

    const characters = computed(() => {
      return store.getters['character/getFeaturedCharacters']
    })

    const charactersLoading = computed(() => {
      return store.getters['character/getCharactersLoadingState']
    })

    watch(characters, (newVal) => {
      if (newVal.length < 8) {
        store.dispatch('character/loadFeaturedCharacters')
      }
    })

    const latestComics = computed(() => {
      return store.getters['comic/getLatestComics']
    })

    const comicsLoading = computed(() => {
      return store.getters['comic/getComicsLoadingState']
    })

    onMounted(() => {
      if(characters.value.length < 8) {
        store.dispatch('character/loadFeaturedCharacters')
      }
    })

    return {
      featuredCharacters: characters,
      latestComics,
      charactersLoading,
      comicsLoading,
    }
  },
}
</script>
