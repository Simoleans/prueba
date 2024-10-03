<template>
  <div class="slider-container">
    <div class="slider" :style="{ transform: `translateX(-${activeIndex * 100}%)` }">
      <div class="slide" v-for="(slide, index) in slides" :key="index">
        <img :src="slide.image" alt="Slider image" class="slide-image" />
        <p class="slide-text">{{ slide.text }}</p>
        <!-- Botón en la última diapositiva -->
        <a
          v-if="index === slides.length - 1"
          href="javascript:void(0)"
          @click="confirmMessage"
          class="magic-button"
        >
          Enviar un mensajito a esta persona arrepentida
        </a>
      </div>
    </div>
    <!-- Botones de navegación -->
    <div class="controls">
      <button @click="prevSlide" class="control-button">Anterior</button>
      <button @click="nextSlide" class="control-button">Siguiente</button>
    </div>
  </div>
  <!-- Canvas para partículas -->
  <canvas ref="canvas" class="particle-canvas"></canvas>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const slides = ref([
  { image: '/1.webp', text: 'Quiero que sepas' },
  { image: '/2.webp', text: 'Que te adoro mucho....  Te extra;o mucho. Extra;o tus mordiscos tu voz, todo' },
  { image: '/4.jpeg', text: 'Si me pongo a poner todo lo que siento por ti no termino nunca. Lo siento mucho amorcito :(' },
  
])

const activeIndex = ref(0)
let interval = null
const canvas = ref(null)
const particles = []
const particleCount = 100

const whatsappLink = 'https://wa.me/584144873548?text=Hola%20quiero%20saber%20más!'

const nextSlide = () => {
  activeIndex.value = (activeIndex.value + 1) % slides.value.length
  triggerParticles() // Activa las partículas al cambiar de diapositiva
}

const prevSlide = () => {
  activeIndex.value = (activeIndex.value - 1 + slides.value.length) % slides.value.length
  triggerParticles() // Activa las partículas al cambiar de diapositiva
}

const confirmMessage = () => {
  if (confirm("Se que en el más fondo del fondo de tu corazón aún me quieres")) {
    window.open(whatsappLink, '_blank')
  }
}

const triggerParticles = () => {
  const ctx = canvas.value.getContext('2d')
  const width = canvas.value.width
  const height = canvas.value.height
  particles.length = 0

  for (let i = 0; i < particleCount; i++) {
    particles.push({
      x: Math.random() * width,
      y: Math.random() * height,
      size: Math.random() * 5 + 1,
      speedX: Math.random() * 3 - 1.5,
      speedY: Math.random() * 3 - 1.5,
    })
  }

  const updateParticles = () => {
    ctx.clearRect(0, 0, width, height)

    particles.forEach(particle => {
      particle.x += particle.speedX
      particle.y += particle.speedY

      if (particle.x < 0 || particle.x > width) particle.speedX *= -1
      if (particle.y < 0 || particle.y > height) particle.speedY *= -1

      ctx.fillStyle = 'rgba(255, 255, 255, 0.8)'
      ctx.beginPath()
      ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2)
      ctx.fill()
    })

    requestAnimationFrame(updateParticles)
  }

  updateParticles()
}

onMounted(() => {
  canvas.value.width = window.innerWidth
  canvas.value.height = window.innerHeight
  interval = setInterval(() => {
    nextSlide()
  }, 5000) // Cambia de diapositiva cada 5 segundos
})

onUnmounted(() => {
  clearInterval(interval)
})
</script>

<style scoped>
.slider-container {
  position: relative;
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  overflow: hidden;
}

.slider {
  display: flex;
  transition: transform 0.5s ease-in-out;
  width: 100%;
}

.slide {
  min-width: 100%;
  box-sizing: border-box;
}

.slide-image {
  width: 100%;
  height: auto;
}

.slide-text {
  text-align: center;
  padding: 1rem;
  font-size: 75px;
  font-weight: bold;
}

.magic-button {
  display: block;
  margin: 1rem auto;
  padding: 0.5rem 1rem;
  background-color: #25D366;
  color: white;
  text-align: center;
  text-decoration: none;
  border-radius: 5px;
  font-size: 1rem;
  transition: background-color 0.3s;
}

.magic-button:hover {
  background-color: #128C7E;
}

.controls {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  display: flex;
  justify-content: center;
  gap: 20px;
  padding: 10px 0;
  background-color: rgba(0, 0, 0, 0.5);
}

.control-button {
  background-color: rgba(255, 255, 255, 0.8);
  color: black;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
  font-size: 14px;
  border-radius: 5px;
  transition: background-color 0.3s;
}

.control-button:hover {
  background-color: rgba(255, 255, 255, 1);
}

.particle-canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

@media (max-width: 600px) {
  .slide-text {
    font-size: 1.1rem;
  }

  .magic-button {
    font-size: 0.9rem;
    padding: 0.4rem 0.8rem;
  }

  .control-button {
    font-size: 12px;
    padding: 5px 10px;
  }
}
</style>
