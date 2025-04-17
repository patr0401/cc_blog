---
title: 3D import
published_at: 2025-3-04
snippet: 
disable_html_sanitization: true
allow_math: true
---

<div id="three.js_container"></div>

<script type="module">

      import * as THREE from "/scripts/threejs/three.js"
   import { OrbitControls } from "/scripts/threejs/OrbitControls.js"
   import codeblockRenderer from "/scripts/codeblock_renderer.js"
   
   const div = document.getElementById ("shader_example")
   const width = div.parentNode.scrollWidth
   const height = Math.floor (width * 9 / 16)
   
   // Basic three.js setup
   const scene = new THREE.Scene ()
   const camera = new THREE.PerspectiveCamera (70, width / height, 0.01, 10)
   camera.position.z = 1
   
   const renderer = new THREE.WebGLRenderer ({ antialias: true })
   renderer.setSize (width, height)
   div.appendChild (renderer.domElement)
   
   const controls = new OrbitControls (camera, renderer.domElement)
   
   // Our shader material
   const shaderMaterial = new THREE.ShaderMaterial({
      uniforms: {
         u_time: { value: 0.0 }
      },
      vertexShader: `
         void main () {
            gl_Position = projectionMatrix * modelViewMatrix * vec4 (position, 1.0);
         }
      `,
      fragmentShader: `
         uniform float u_time;
         
         void main () {
            vec2 uv = gl_FragCoord.xy / vec2 (${ width }.0, ${ height }.0);
            
            // Create a pulsing effect based on time
            float r = 0.5 + 0.5 * sin (u_time + uv.x * 6.0);
            float g = 0.5 + 0.5 * sin (u_time + uv.y * 6.0);
            float b = 0.5 + 0.5 * sin (u_time + uv.x * 2.0 + uv.y * 2.0);
            
            gl_FragColor = vec4 (r, g, b, 1.0);
         }
      `
   })
   
   // Create a simple plane to display our shader
   const geometry = new THREE.PlaneGeometry (1.6, 0.9)
   const mesh = new THREE.Mesh (geometry, shaderMaterial)
   scene.add (mesh)
   
   // Animation loop
   renderer.setAnimationLoop (ms => {
      shaderMaterial.uniforms.u_time.value = ms * 0.001
      renderer.render (scene, camera)
   })
   
   // Render code block
   codeblockRenderer (document, "shader_example_script", "shader_example_code")

// <script>