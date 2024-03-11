import type { KeenSliderInstance } from 'keen-slider'
<script setup lang="ts">
import { type Ref, ref, computed, onMounted } from 'vue'
import { type KeenSliderOptions, type KeenSliderPlugin, useKeenSlider } from 'keen-slider/vue.es'
import 'keen-slider/keen-slider.min.css'

const slideHoverPercentage = 50

const slidesConfig = ref({
  perView: 5,
  spacing: 10,
})

interface ElementWidth {
  width: string
  maxWidth: string
  minWidth: string
}

interface Slide {
  width: ElementWidth
  bg?: string
  isHovered?: boolean
}

const wait = await new Promise((resolve) => setTimeout(resolve, 1000))

const isTouchDevice = ref(false)

onMounted(() => {
  // Detect if the device supports touch events
  isTouchDevice.value = 'ontouchstart' in document.documentElement
})

const slidesRefs = ref<Ref<HTMLElement>[]>([])
const hoveredColumnIndex = ref(-1)

const sliderOptions = ref<KeenSliderOptions>({
  loop: false,
  mode: 'snap',
  rtl: false,
  // slides: {
  //   perView: 'auto',
  // },
  slides: slidesConfig.value,
  dragged: () => {
    restoreColumns(slides.value)

    // if (hoveredColumnIndex.value !== -1) {
    //   // Fire onmouseleave event for the hovered column
    //   slidesRefs.value[hoveredColumnIndex.value].value.dispatchEvent(new Event('mouseleave'))
    // }
  },
})

// const { useKeenSlider } = await import('keen-slider/vue.es')

const [reference, slider] = useKeenSlider(sliderOptions)

const baseWidth = computed(() => {
  return `calc((100% - ${slidesConfig.value.spacing * (slidesConfig.value.perView - 1)}px) / ${slidesConfig.value.perView})`
})

const columnWidth = computed(() => {
  // Calculate width for hovered column using increase ratio
  const hoveredColumnWidth = slideHoverPercentage

  // Calculate width for other columns substituting hovered column width and considering spacing, use CSS calc() function
  const otherColumnWidth = `calc((100% - ${hoveredColumnWidth}% - ${slidesConfig.value.spacing * (slidesConfig.value.perView - 1)}px) / ${slidesConfig.value.perView - 1})`

  return {
    hovered: {
      width: `${hoveredColumnWidth}%`,
      maxWidth: `${hoveredColumnWidth}%`,
      minWidth: `${hoveredColumnWidth}%`,
    },
    other: {
      width: otherColumnWidth,
      maxWidth: otherColumnWidth,
      minWidth: otherColumnWidth,
    },
  }
})

const restoreColumns = (slides: Slide[]) => {
  if (!slider.value) {
    return
  }

  hoveredColumnIndex.value = -1

  for (let i = 0; i < slidesConfig.value.perView; i++) {
    const relativeIndex = slider.value.track.details.rel + i

    slides[relativeIndex].width = {
      width: baseWidth.value,
      maxWidth: baseWidth.value,
      minWidth: baseWidth.value,
    }
    slides[relativeIndex].bg = 'red'
    slides[relativeIndex].isHovered = false
  }

  // slides.forEach((slide) => {
  //   slide.width = {
  //     width: baseWidth.value,
  //     maxWidth: baseWidth.value,
  //     minWidth: baseWidth.value,
  //   }
  //   slide.bg = 'red'
  //   slide.isHovered = false
  // })
}

const slides = ref<Slide[]>(
  Array.from({ length: 20 }).map((_, idx) => {
    return {
      width: {
        width: baseWidth.value,
        maxWidth: baseWidth.value,
        minWidth: baseWidth.value,
      },
      isHovered: false,
    }
  })
)

// const MutationPlugin: KeenSliderPlugin = (slider) => {
//   const observer = new MutationObserver(function (mutations) {
//     mutations.forEach(function (mutation) {
//       slider.update()
//     })
//   })
//   const config = { childList: true }

//   slider.on('created', () => {
//     observer.observe(slider.container, config)
//   })
//   slider.on('destroyed', () => {
//     observer.disconnect()
//   })
// }

function moreSlides() {
  // sliderOptions.value.slides.perView++
  console.log(slider.value?.track)
}

// function lessSlides() {
//   sliderOptions.value.slides.perView--
//   slider.value?.update()
// }

const expandColumn = (slides: Slide[], index: number) => {
  hoveredColumnIndex.value = index

  if (slides[index].isHovered) {
    return
  }

  console.log('expandColumn', index)
  slides[index].width = columnWidth.value.hovered
  slides[index].isHovered = true

  // slides.forEach((slide, i) => {
  //   if (i !== index) {
  //     slide.width = columnWidth.value.other
  //   }
  // })
  // Adjust the widths of other columns that are in a range of slidesConfig.perView from the hovered column (consider indexes smaller than 0 and bigger than slides.length)
  for (let i = 0; i < slidesConfig.value.perView; i++) {
    const relativeIndex = (slider.value?.track.details.rel ?? 0) + i

    console.log({ relativeIndex, index, rel: slider.value?.track.details.rel, i })

    if (relativeIndex !== index) {
      slides[relativeIndex].width = columnWidth.value.other
      slides[relativeIndex].bg = 'blue'
    }
    // if (index - i >= 0) {
    //   slides[index - i].width = columnWidth.value.other
    // }
    // if (index + i < slides.length) {
    //   slides[index + i].width = columnWidth.value.other
    // }
  }
}
const onTouchStart = (slides: Slide[], index: number) => {
  if (hoveredColumnIndex.value !== -1) {
    restoreColumns(slides)
  }

  expandColumn(slides, index)
}
</script>

<template>
  <div ref="reference" class="keen-slider max-w-full sm:max-w-1/2 bg-green">
    <!-- :style="{ gap: `${slidesConfig.spacing}px` }" -->
    <div
      v-for="(slide, index) in slides"
      :key="index"
      ref="slidesRefs"
      :class="[
        'keen-slider__slide',
        'h-64',
        'max-h-82',
        'flex',
        'items-center',
        'justify-center',
        'bg-red',
        'text-lg',
        'text-primary',
        'font-bold',
        'border',
        // '[transition:width_0.5s_ease]',
        // '[transition:max-width_0.5s_ease]',
        'transition-min-width',
        'transition-duration-500',
        'ease-in-out',
      ]"
      :style="{ ...slide.width, background: slide.bg || 'red' }"
      @mouseenter="() => (isTouchDevice ? null : expandColumn(slides, index))"
      @mouseleave="() => (isTouchDevice ? null : restoreColumns(slides))"
      @touchstart="onTouchStart(slides, index)"
    >
      Slide: {{ index + 1 }}
    </div>
  </div>

  <!-- <button class="bg-white text-black w-30" @click="moreSlides">More slides</button>
  <button class="bg-white text-black w-30" @click="lessSlides">Less slides</button> -->
</template>
