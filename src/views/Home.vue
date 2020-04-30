<template>
  <div class="flex flex-col justify-center items-center fixed w-full h-full overflow-hidden">
    <div class="flex fixed h-full bg-black bg-opacity-50 text-white shadow transition-all duration-500" :style="{ left: showMenu ? '0' : '-16rem' }">
      <div class="py-6 pl-6 w-64 h-full">
        <div class="pb-6 font-bold uppercase tracking-widest">Settings</div>
        <div class="flex justify-between items-center py-2">
          <span class="text-xs uppercase tracking-wider">Kaleidoscope</span>
          <button class="flex flex-col justify-center relative w-12 h-6 rounded-full bg-white focus:outline-none" @click="() => { config.kaleidoscope = ! config.kaleidoscope; }">
            <div class="absolute w-6 h-6 rounded-full border-2 border-white bg-gray-400 transition-all duration-500" :class="{ 'bg-gray-600': config.kaleidoscope }" :style="{ left: config.kaleidoscope ? '1.5rem' : '0' }"></div>
          </button>
        </div>
        <div class="flex justify-between items-center py-2">
          <span class="text-xs uppercase tracking-wider">Color</span>
          <label class="w-12 h-6 rounded" :style="{ backgroundColor: config.color }">
            <input type="color" class="hidden" v-model="config.color" @change="(e) => { config.color = e.target.value; start(); }" />
          </label>
        </div>
        <div class="flex justify-between items-center py-2">
          <span class="text-xs uppercase tracking-wider">3D Objects</span>
          <div class="flex">
            <button class="w-6 bg-gray-500 rounded-l focus:outline-none" @click="() => { config.number = config.number - 1; start(); }">-</button>
            <div class="w-12 bg-white text-gray-800 text-center leading-normal">{{ config.number }}</div>
            <button class="w-6 bg-gray-500 rounded-r focus:outline-none" @click="() => { config.number = config.number + 1; start(); }">+</button>
          </div>
        </div>
        <div class="flex justify-between items-center py-2">
          <span class="text-xs uppercase tracking-wider">Boundaries</span>
          <div class="flex">
            <button class="w-6 bg-gray-500 rounded-l focus:outline-none" @click="() => { config.boundaries = config.boundaries - 1; start(); }">-</button>
            <div class="w-12 bg-white text-gray-800 text-center leading-normal">{{ config.boundaries }}</div>
            <button class="w-6 bg-gray-500 rounded-r focus:outline-none" @click="() => { config.boundaries = config.boundaries + 1; start(); }">+</button>
          </div>
        </div>
        <div class="flex justify-between items-center py-2">
          <span class="text-xs uppercase tracking-wider">Size</span>
          <div class="flex">
            <button class="w-6 bg-gray-500 rounded-l focus:outline-none" @click="() => { config.size = config.size - 1; start(); }">-</button>
            <div class="w-12 bg-white text-gray-800 text-center leading-normal">{{ config.size }}</div>
            <button class="w-6 bg-gray-500 rounded-r focus:outline-none" @click="() => { config.size = config.size + 1; start(); }">+</button>
          </div>
        </div>
        <div class="flex justify-between items-center py-2">
          <span class="text-xs uppercase tracking-wider">Sides</span>
          <div class="flex">
            <button class="w-6 bg-gray-500 rounded-l focus:outline-none" @click="() => { config.sides = config.sides - 1; start(); }">-</button>
            <div class="w-12 bg-white text-gray-800 text-center leading-normal">{{ config.sides }}</div>
            <button class="w-6 bg-gray-500 rounded-r focus:outline-none" @click="() => { config.sides = config.sides + 1; start(); }">+</button>
          </div>
        </div>
        <div class="flex justify-between items-center py-2">
          <span class="text-xs uppercase tracking-wider">Angle</span>
          <div class="flex">
            <button class="w-6 bg-gray-500 rounded-l focus:outline-none" @click="() => { config.angle = config.angle - 1; start(); }">-</button>
            <div class="w-12 bg-white text-gray-800 text-center leading-normal">{{ config.angle }}</div>
            <button class="w-6 bg-gray-500 rounded-r focus:outline-none" @click="() => { config.angle = config.angle + 1; start(); }">+</button>
          </div>
        </div>
      </div>
      <div class="p-6">
        <button class="flex flex-col justify-between h-4 focus:outline-none" @click="showMenu = ! showMenu">
          <div class="p-px w-6 bg-white rounded-full"></div>
          <div class="p-px w-6 bg-white rounded-full"></div>
          <div class="p-px w-6 bg-white rounded-full"></div>
        </button>
      </div>
    </div>
    <canvas ref="canvas"></canvas>
  </div>
</template>

<script>
  import * as THREE from 'three'
  import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer.js'
  import { RenderPass } from 'three/examples/jsm/postprocessing/RenderPass.js'
  import { ShaderPass } from 'three/examples/jsm/postprocessing/ShaderPass.js'
  import { KaleidoShader } from 'three/examples/jsm/shaders/KaleidoShader.js'

  export default {
    data () {
      return {
        showMenu: true,
        scene: null,
        camera: null,
        renderer: null,
        geometry: null,
        material: null,
        group: null,
        light: null,
        composer: null,
        config: {
          number: 20,
          color: '#E53E3E',
          boundaries: 10,
          size: 3,
          kaleidoscope: true,
          sides: 6,
          angle: 45,
        }
      }
    },
    methods: {
      animate () {
        this.animation = requestAnimationFrame(this.animate)

        this.group.rotation.x += 0.01
        this.group.rotation.y += 0.01
        this.group.rotation.z += 0.01

        this.renderScene()
      },
      renderScene () {
        if (this.config.kaleidoscope) {
          this.composer.render()
        } else {
          this.renderer.render(this.scene, this.camera)
        }
      },
      start () {
        this.scene = new THREE.Scene()
        this.camera = new THREE.PerspectiveCamera(75, 1, 0.1, 1000)

        this.renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true, canvas: this.$refs.canvas })
        this.renderer.setSize(window.innerWidth, window.innerHeight)

        this.geometry = new THREE.IcosahedronGeometry(this.config.size, 0)

        this.material = new THREE.MeshStandardMaterial({ color: this.config.color })

        this.group = new THREE.Object3D()

        for (let i = 0; i < this.config.number; i++) {
          let mesh = new THREE.Mesh(this.geometry, this.material)
          let b = this.config.boundaries
          mesh.position.set(THREE.Math.randInt(-b, b), THREE.Math.randInt(-b, b), THREE.Math.randInt(-b, b))
          this.group.add(mesh)
        }

        this.scene.add(this.group)

        this.light = new THREE.DirectionalLight(0xFFFFFF)
        this.light.position.set(0, 0, 250)
        this.scene.add(this.light)

        this.camera.position.set(0, 0, 40)

        if (this.config.kaleidoscope) {
          let renderTarget = new THREE.WebGLRenderTarget(window.innerWidth, window.innerHeight, {
            minFilter: THREE.LinearFilter, 
            magFilter: THREE.LinearFilter, 
            format: THREE.RGBAFormat, 
            stencilBuffer: false
          })

          this.composer = new EffectComposer(this.renderer, renderTarget)
          this.composer.addPass(new RenderPass(this.scene, this.camera))

          let effect = new ShaderPass(KaleidoShader)
          effect.uniforms.sides.value = this.config.sides
          effect.uniforms['angle'].value = this.config.angle * Math.PI / 180
          this.composer.addPass(effect)

          effect.renderToScreen = true
        }
      },
    },
    mounted () {
      this.start()
      this.animate()

      window.addEventListener('resize', () => {
        this.camera.updateProjectionMatrix()
        this.renderer.setSize(window.innerWidth, window.innerHeight)
      })
    },
    beforeDestroy() {
      cancelAnimationFrame(this.animate)
    }
  }
</script>
