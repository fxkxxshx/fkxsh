<template>
  <div id="chara" ref="chara"></div>
</template>

<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import { VRM, VRMSchema } from '@pixiv/three-vrm';

export default {
  name: 'Chara',
  data () {
    // renderer
    const renderer = new THREE.WebGLRenderer();
    // camera
    const camera = new THREE.PerspectiveCamera(20.0, window.innerWidth / window.innerHeight, 0.1, 20.0);
    // scene
    const scene = new THREE.Scene();
    // light
    const light = new THREE.DirectionalLight(0xffffff);
    // gltf and vrm
    const loader = new GLTFLoader();
    // mouse
    let mouseX = 0;
    let mouseY = 0;
    // window half
    let windowHalfX = window.innerWidth / 2;
    let windowHalfY = window.innerHeight / 2;

    return {
      renderer: renderer,
      camera: camera,
      scene: scene,
      light: light,
      loader: loader,
      mouseX: mouseX,
      mouseY: mouseY,
      windowHalfX: windowHalfX,
      windowHalfY: windowHalfY,
    };
  },
  mounted () {
    this.initialize();
  },
  methods: {
    initialize ()  {
      // renderer
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.renderer.setClearColor(0xF5F5F5, 1);
      this.$refs.chara.appendChild(this.renderer.domElement);
      // camera
      this.camera.position.x = 0.0;
      this.camera.position.y = 0.7;
      this.camera.position.z = 5.0;
      // light
      this.light.position.set(1.0, 1.0, 1.0).normalize();
      this.scene.add(this.light);
      // gltf and vrm
      this.loader.crossOrigin = 'anonymous';
      this.loader.load(
        '/chara/fkxsh.vrm',
        (gltf) => {
          VRM.from(gltf).then((vrm) => {
              this.scene.add(vrm.scene);
              vrm.humanoid.getBoneNode(VRMSchema.HumanoidBoneName.Hips).rotation.y = Math.PI;
              vrm.humanoid.getBoneNode(VRMSchema.HumanoidBoneName.LeftUpperArm).rotation.z = Math.PI / 2 - 0.3;
              vrm.humanoid.getBoneNode(VRMSchema.HumanoidBoneName.RightUpperArm).rotation.z = -(Math.PI / 2 - 0.3);
              vrm.humanoid.getBoneNode(VRMSchema.HumanoidBoneName.LeftHand).rotation.z = 0.1;
              vrm.humanoid.getBoneNode(VRMSchema.HumanoidBoneName.RightHand).rotation.z = -0.1;
          });
        }
      );

      document.addEventListener('mousemove', this.onDocumentMouseMove);
      window.addEventListener('resize', this.onResize);
      window.addEventListener('deviceorientation', this.onGyro);

      this.onResize();
      this.animate();
    },
    animate () {
      requestAnimationFrame(this.animate);

      this.camera.position.x += (this.mouseX - this.camera.position.x) * .00035;
      this.camera.position.y += (- this.mouseY - this.camera.position.y) * .00035;

      if (this.camera.position.x > 2.5) {
        this.camera.position.x = 2.5;
      }
      if (this.camera.position.x < -2.5) { 
        this.camera.position.x = -2.5;
      }

      if (this.camera.position.y > 3.2) {
        this.camera.position.y = 3.2;
      }
      if (this.camera.position.y < -1.8) { 
        this.camera.position.y = -1.8;
      }

      this.camera.lookAt(new THREE.Vector3(0, 0.7, 0));
      this.renderer.render(this.scene, this.camera );
    }, 
    onDocumentMouseMove (event) {
      this.mouseX = (event.clientX - this.windowHalfX) / 2;
      this.mouseY = (event.clientY - this.windowHalfY) / 2;
    },
    onResize () {
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
    },
    onGyro (event) {
      this.mouseX = (event.beta - this.windowHalfX) / 2;
      this.mouseY = (event.gamma - this.windowHalfY) / 2;
    }
  }
}
</script>

<style scoped lang="scss">
@import "@/assets/scss/var.scss";
@import "@/assets/scss/mixin.scss";

#chara {
  position: fixed;
  left: 0;
  top: 0;
  z-index: -1;
}
</style>