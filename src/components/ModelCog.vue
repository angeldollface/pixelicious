<!--
PIXELICIOUS by Alexander Abraham, a.k.a. "Angel Dollface".
Licensed under the MIT license.
-->

<script lang="ts">
// importing standard Vue methods
// for Typescript.
import { defineComponent } from 'vue';

// Standard three.js import.
import * as THREE from 'three';

// Importing the GLTF 2.0 loader.
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';

// Importing the shader for object glow-up.
import { RenderPass } from 'three/examples/jsm/postprocessing/RenderPass';

// Importing the "composer" to slap a filter on our scene.
import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer';

// Importing the filter.
import { UnrealBloomPass } from 'three/examples/jsm/postprocessing/UnrealBloomPass';

export default defineComponent(
  {
    name: 'ModelCog',
    methods: {
      // A function to render our models.
      renderModel(): void {

        // We make a new three.js scene.
        let scene: THREE.Scene = new THREE.Scene();

        // We make a new camera. Something has
        // to look at our scene.
        let camera: THREE.PerspectiveCamera = new THREE.PerspectiveCamera(
          75, // Angle.
          window.innerWidth/window.innerHeight, // FOV.
          0.01,
          1000
        );

        // Setting the background color.
        scene.background = new THREE.Color(0x000000);

        // Something has to render our scene.
        // We use the "standard" WebGL renderer.
        var renderer = new THREE.WebGLRenderer(
          {
            antialias: true
          }
        );

        // We set the width of and height of the renderer.
        renderer.setSize(window.innerWidth, window.innerHeight);

        // We add the renderer to the document.
        document.body.appendChild(renderer.domElement);

        // Setting up everything to use a shader.
        const renderScene = new RenderPass( scene, camera );

        // Parameters for making named objects emit light.
        const params = {
          exposure: 0,
          bloomStrength: 1.5,
          bloomThreshold: 0.1,
          bloomRadius: 1
        };

        // A shader to make individual meshes glow up. (Pillars on the corners.)
        const bloomPass = new UnrealBloomPass( new THREE.Vector2( window.innerWidth, window.innerHeight ), 1.5, 0.4, 0.85 );
        bloomPass.threshold = params.bloomThreshold;
        bloomPass.strength = params.bloomStrength;
        bloomPass.radius = params.bloomRadius;

        // We use a composer instead of the standard renderer
        // and add relevant data, because the shader is a "filter"
        // on the scene.
        var composer = new EffectComposer( renderer );
        composer.addPass( renderScene );
        composer.addPass( bloomPass );

        // Since the model is external we need to load it in.
        // For this we use the GLTF 2.0 loader three.js gives us.
        var loader = new GLTFLoader();

        // Empty variables to
        // populate with meshes from the loaded model.
        let name: any;

        let lightOne: any;
        let lightTwo: any;
        let lightThree: any;

        // Closure to load a model from a path (remote or local).
        loader.load(
          'https://angeldollface.art/doll-cdn/models/neonLightsExp.glb',
          function(gltf){
            // Model size.
            gltf.scene.scale.set( 1, 1, 1 );
      
            // Model pos.
            gltf.scene.position.x = 0;                   
            gltf.scene.position.y = 0;
            gltf.scene.position.z = 0;   
            gltf.scene.rotation.y = 0; 

            // We add it to our scene...
            scene.add(gltf.scene);

            // Getting the name mesh and making it glow.
            name = gltf.scene.getObjectByName('name');
            name.material.emissive = new THREE.Color(0xEA1573);
            name.material.emissiveIntensity = defaultEmissiveIntensity;

            // Getting the light meshes and making them glow.

            // Top border light.
            lightOne = gltf.scene.getObjectByName('Light01');
            lightOne.material.emissive = new THREE.Color(0xFFFFFF);
            lightOne.material.emissiveIntensity = borderLightIntensity;

            // Right border light.
            lightTwo = gltf.scene.getObjectByName('Light02');
            lightTwo.material.emissive = new THREE.Color(0xFFFFFF);
            lightTwo.material.emissiveIntensity = borderLightIntensity;

            // Left border light.
            lightThree = gltf.scene.getObjectByName('Light03');
            lightThree.material.emissive = new THREE.Color(0xFFFFFF);
            lightThree.material.emissiveIntensity = borderLightIntensity;

            // We call the animation function
            // otherwise nothing happens.
            animate();
          }
        );

        // We set up the camera position.
        camera.position.set(0,1.5,3);

        // Setting the rotation.
        camera.rotation.set(0,0,0);

        // The default setting for the intensitivity of the text glow.
        const defaultEmissiveIntensity:number = 6;

        // Intensity of the border lights.
        const borderLightIntensity: number = 10;

        // Default rotation speed.
        const rotationSpeed: number = 0.07;

        // And animate the whole scene
        // recursively.
        const animate = () => {

          // Adding an event listener to resize
          // the scene for different devices.
          window.addEventListener(
            'resize', 
            resize 
          );

          // Re-renders the scene on
          // every frame bounce.
          composer.render();

          // Rotating the top light.
          lightOne.rotation.y += rotationSpeed;

          // Rotating the right border light.
          lightTwo.rotation.x += rotationSpeed;

          // Rotating the left border light.
          lightThree.rotation.x += rotationSpeed;

          // The "frame bounce".
          requestAnimationFrame(animate);
        }

        // Defining a nested function to adjust some renderer
        // and camera settings.
        const resize = () => {
          if (window.innerWidth < 800){
            camera.position.z = 10;
          }
          else {
            // Do nothing.
          }
          renderer.setPixelRatio(window.devicePixelRatio);
          renderer.setSize(window.innerWidth, window.innerHeight);
          camera.aspect = window.innerWidth / window.innerHeight;
          camera.updateProjectionMatrix();
          composer.setSize(window.innerWidth, window.innerHeight);
        }

      }
    },
    mounted() {
      this.renderModel();
    }
  },
);
</script>
