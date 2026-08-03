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
    return {
      renderer: renderer,
      camera: camera,
      scene: scene,
      light: light,
      loader: loader
    };
  },
  mounted () {
    this.clock = new THREE.Clock();
    this.vrm = null;
    this.idleBones = {};
    this.idlePose = {};
    this.groundLevel = null;
    this.contactShadow = null;
    this.shadowGroundY = null;
    this.shadowReferenceHipsY = null;
    this.cameraPointer = new THREE.Vector2();
    this.cameraBasePosition = new THREE.Vector3(0, 1, 6);
    this.cameraTargetPosition = this.cameraBasePosition.clone();
    this.onThemeChange = this.updateTheme.bind(this);
    window.addEventListener('themechange', this.onThemeChange);
    this.nextBlinkAt = 1.5 + Math.random() * 2;
    this.blinkStartedAt = null;
    this.initialize();
  },
  beforeDestroy () {
    cancelAnimationFrame(this.animationFrameId);
    window.removeEventListener('mousemove', this.onMouseMove);
    window.removeEventListener('touchstart', this.onTouch);
    window.removeEventListener('touchmove', this.onTouch);
    document.removeEventListener('mouseleave', this.onMouseLeave);
    window.removeEventListener('resize', this.onResize);
    window.removeEventListener('themechange', this.onThemeChange);
    if (this.contactShadow) {
      this.scene.remove(this.contactShadow);
      this.contactShadow.geometry.dispose();
      this.contactShadow.material.map.dispose();
      this.contactShadow.material.dispose();
    }
    this.renderer.dispose();
  },
  methods: {
    initialize ()  {
      // renderer
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.updateTheme();
      this.$refs.chara.appendChild(this.renderer.domElement);
      // camera
      this.camera.position.x = 0.0;
      this.camera.position.y = 1.0;
      this.camera.position.z = 6.0;
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
              this.setupIdleAnimation(vrm);
          });
        }
      );

      window.addEventListener('mousemove', this.onMouseMove, { passive: true });
      window.addEventListener('touchstart', this.onTouch, { passive: true });
      window.addEventListener('touchmove', this.onTouch, { passive: true });
      document.addEventListener('mouseleave', this.onMouseLeave);
      window.addEventListener('resize', this.onResize);

      this.onResize();
      this.animate();
    },
    setupIdleAnimation (vrm) {
      const boneNames = {
        hips: VRMSchema.HumanoidBoneName.Hips,
        spine: VRMSchema.HumanoidBoneName.Spine,
        chest: VRMSchema.HumanoidBoneName.Chest,
        neck: VRMSchema.HumanoidBoneName.Neck,
        head: VRMSchema.HumanoidBoneName.Head,
        leftShoulder: VRMSchema.HumanoidBoneName.LeftShoulder,
        rightShoulder: VRMSchema.HumanoidBoneName.RightShoulder,
        leftUpperArm: VRMSchema.HumanoidBoneName.LeftUpperArm,
        rightUpperArm: VRMSchema.HumanoidBoneName.RightUpperArm,
        leftLowerArm: VRMSchema.HumanoidBoneName.LeftLowerArm,
        rightLowerArm: VRMSchema.HumanoidBoneName.RightLowerArm,
        leftHand: VRMSchema.HumanoidBoneName.LeftHand,
        rightHand: VRMSchema.HumanoidBoneName.RightHand,
        leftUpperLeg: VRMSchema.HumanoidBoneName.LeftUpperLeg,
        rightUpperLeg: VRMSchema.HumanoidBoneName.RightUpperLeg,
        leftLowerLeg: VRMSchema.HumanoidBoneName.LeftLowerLeg,
        rightLowerLeg: VRMSchema.HumanoidBoneName.RightLowerLeg,
        leftFoot: VRMSchema.HumanoidBoneName.LeftFoot,
        rightFoot: VRMSchema.HumanoidBoneName.RightFoot,
        leftToes: VRMSchema.HumanoidBoneName.LeftToes,
        rightToes: VRMSchema.HumanoidBoneName.RightToes
      };

      const sides = ['Left', 'Right'];
      const fingers = ['Thumb', 'Index', 'Middle', 'Ring', 'Little'];
      const joints = ['Proximal', 'Intermediate', 'Distal'];
      sides.forEach((side) => {
        fingers.forEach((finger) => {
          joints.forEach((joint) => {
            const schemaName = `${side}${finger}${joint}`;
            const key = `${side.toLowerCase()}${finger}${joint}`;
            boneNames[key] = VRMSchema.HumanoidBoneName[schemaName];
          });
        });
      });

      Object.keys(boneNames).forEach((key) => {
        const bone = vrm.humanoid.getBoneNode(boneNames[key]);
        if (bone) {
          this.idleBones[key] = bone;
          this.idlePose[key] = {
            position: bone.position.clone(),
            rotation: bone.rotation.clone()
          };
        }
      });

      this.vrm = vrm;
      vrm.scene.updateMatrixWorld(true);
      this.groundLevel = this.getLowestFootPosition();
      this.setupContactShadow(vrm);
      this.clock.start();
    },
    setupContactShadow (vrm) {
      const canvas = document.createElement('canvas');
      canvas.width = 128;
      canvas.height = 128;
      const context = canvas.getContext('2d');
      const gradient = context.createRadialGradient(64, 64, 2, 64, 64, 60);
      gradient.addColorStop(0, 'rgba(35, 35, 35, 0.48)');
      gradient.addColorStop(0.45, 'rgba(50, 50, 50, 0.24)');
      gradient.addColorStop(1, 'rgba(70, 70, 70, 0)');
      context.fillStyle = gradient;
      context.fillRect(0, 0, canvas.width, canvas.height);

      const bounds = new THREE.Box3().setFromObject(vrm.scene);
      const texture = new THREE.CanvasTexture(canvas);
      const geometry = new THREE.PlaneGeometry(0.66, 0.34);
      const material = new THREE.MeshBasicMaterial({
        map: texture,
        transparent: true,
        opacity: 0.42,
        depthWrite: false,
        side: THREE.DoubleSide
      });

      this.contactShadow = new THREE.Mesh(geometry, material);
      this.contactShadow.rotation.x = -Math.PI / 2 + 0.22;
      this.contactShadow.renderOrder = -1;
      this.shadowGroundY = bounds.min.y;
      this.shadowReferenceHipsY = this.idleBones.hips
        .getWorldPosition(new THREE.Vector3()).y;
      this.scene.add(this.contactShadow);
      this.updateContactShadow();
    },
    updateIdleAnimation (elapsed) {
      if (!this.vrm || !this.idleBones.hips) return;

      // Several slightly different cycles keep the idle pose from looking mechanical.
      const breath = Math.sin(elapsed * 1.45);
      const sway = Math.sin(elapsed * 0.52);
      const weightShift = Math.sin(elapsed * 0.37 + 0.8);
      const look = Math.sin(elapsed * 0.29 + 1.7);
      const handMotion = (Math.sin(elapsed * 0.78 + 0.45) + 1) / 2;
      const legMotion = Math.sin(elapsed * 0.46 + 1.1);
      const stanceShift = legMotion;

      const setRotation = (name, x, y, z) => {
        const bone = this.idleBones[name];
        const pose = this.idlePose[name];
        if (bone && pose) {
          bone.rotation.set(
            pose.rotation.x + x,
            pose.rotation.y + y,
            pose.rotation.z + z
          );
        }
      };

      const hips = this.idleBones.hips;
      const hipsPose = this.idlePose.hips;
      hips.position.set(
        hipsPose.position.x + sway * 0.006 + stanceShift * 0.018,
        hipsPose.position.y + breath * 0.012,
        hipsPose.position.z
      );
      setRotation('hips', breath * 0.016, 0, weightShift * 0.008 + stanceShift * 0.02);
      setRotation('spine', -breath * 0.016, sway * 0.012, -stanceShift * 0.003);
      setRotation('chest', -breath * 0.022, sway * 0.014, -stanceShift * 0.002);
      setRotation('neck', breath * 0.008, look * 0.014, -weightShift * 0.008);
      setRotation('head', breath * 0.012, look * 0.022, -weightShift * 0.012);
      setRotation('leftShoulder', breath * 0.018, sway * 0.014, weightShift * 0.025);
      setRotation('rightShoulder', -breath * 0.018, -sway * 0.014, weightShift * 0.025);
      setRotation('leftUpperArm', breath * 0.05, weightShift * 0.026, breath * 0.038);
      setRotation('rightUpperArm', -breath * 0.05, -weightShift * 0.026, -breath * 0.038);
      setRotation('leftLowerArm', handMotion * 0.025, -0.07 - handMotion * 0.07, breath * 0.018);
      setRotation('rightLowerArm', -handMotion * 0.025, 0.07 + handMotion * 0.07, -breath * 0.018);
      setRotation('leftHand', breath * 0.035, handMotion * 0.045, handMotion * 0.075);
      setRotation('rightHand', -breath * 0.035, -handMotion * 0.045, -handMotion * 0.075);

      // Alternate the supporting leg, soften the knees, and counter-rotate the feet.
      const leftKneeBend = 0.06 + (legMotion + 1) * 0.12;
      const rightKneeBend = 0.06 + (1 - legMotion) * 0.12;
      setRotation('leftUpperLeg', -leftKneeBend * 0.5, 0, -stanceShift * 0.025);
      setRotation('rightUpperLeg', -rightKneeBend * 0.5, 0, -stanceShift * 0.025);
      setRotation('leftLowerLeg', leftKneeBend, 0, stanceShift * 0.015);
      setRotation('rightLowerLeg', rightKneeBend, 0, stanceShift * 0.015);
      setRotation('leftFoot', -leftKneeBend * 0.58, 0, stanceShift * 0.022);
      setRotation('rightFoot', -rightKneeBend * 0.58, 0, stanceShift * 0.022);
      setRotation('leftToes', leftKneeBend * 0.22, 0, 0);
      setRotation('rightToes', rightKneeBend * 0.22, 0, 0);

      // Keep the fingers loosely curled and gently open and close each hand.
      const fingerCurl = 0.18 + handMotion * 0.2;
      const fingerOffsets = {
        Index: 0,
        Middle: 0.025,
        Ring: 0.055,
        Little: 0.085
      };
      const jointStrength = {
        Proximal: 1,
        Intermediate: 0.82,
        Distal: 0.58
      };

      ['left', 'right'].forEach((side) => {
        const direction = side === 'left' ? 1 : -1;
        ['Index', 'Middle', 'Ring', 'Little'].forEach((finger) => {
          ['Proximal', 'Intermediate', 'Distal'].forEach((joint) => {
            const curl = (fingerCurl + fingerOffsets[finger]) * jointStrength[joint];
            setRotation(`${side}${finger}${joint}`, 0, 0, direction * curl);
          });
        });

        const thumbCurl = 0.1 + handMotion * 0.14;
        setRotation(`${side}ThumbProximal`, 0, direction * thumbCurl * 0.45, direction * thumbCurl);
        setRotation(`${side}ThumbIntermediate`, 0, 0, direction * thumbCurl * 0.85);
        setRotation(`${side}ThumbDistal`, 0, 0, direction * thumbCurl * 0.55);
      });

      this.keepFeetGrounded();
      this.updateContactShadow();
      this.updateBlink(elapsed);
    },
    getLowestFootPosition () {
      const footPosition = new THREE.Vector3();
      const heights = ['leftFoot', 'rightFoot']
        .filter((name) => this.idleBones[name])
        .map((name) => this.idleBones[name].getWorldPosition(footPosition.clone()).y);

      return heights.length ? Math.min(...heights) : null;
    },
    keepFeetGrounded () {
      if (this.groundLevel === null) return;

      this.vrm.scene.updateMatrixWorld(true);
      const lowestFoot = this.getLowestFootPosition();
      if (lowestFoot !== null) {
        this.vrm.scene.position.y += this.groundLevel - lowestFoot;
      }
    },
    updateContactShadow () {
      if (!this.contactShadow || this.shadowGroundY === null) return;

      this.vrm.scene.updateMatrixWorld(true);
      const leftFoot = this.idleBones.leftFoot;
      const rightFoot = this.idleBones.rightFoot;
      if (!leftFoot || !rightFoot) return;

      const leftPosition = leftFoot.getWorldPosition(new THREE.Vector3());
      const rightPosition = rightFoot.getWorldPosition(new THREE.Vector3());
      const hipsPosition = this.idleBones.hips.getWorldPosition(new THREE.Vector3());
      const footDistance = Math.abs(leftPosition.x - rightPosition.x);
      const verticalOffset = hipsPosition.y - this.shadowReferenceHipsY;
      const heightFactor = THREE.MathUtils.clamp(verticalOffset / 0.08, -1, 1);
      const heightScale = 1 - heightFactor * 0.1;
      this.contactShadow.position.set(
        (leftPosition.x + rightPosition.x) / 2,
        this.shadowGroundY + 0.012,
        (leftPosition.z + rightPosition.z) / 2 - 0.035
      );
      this.contactShadow.scale.set(
        (0.9 + Math.min(footDistance, 0.4) * 0.5) * heightScale,
        heightScale,
        1
      );
      this.contactShadow.material.opacity = 0.42 - heightFactor * 0.07;
    },
    updateBlink (elapsed) {
      const blendShape = this.vrm && this.vrm.blendShapeProxy;
      if (!blendShape) return;

      const blinkDuration = 0.16;
      if (this.blinkStartedAt === null && elapsed >= this.nextBlinkAt) {
        this.blinkStartedAt = elapsed;
      }

      if (this.blinkStartedAt !== null) {
        const progress = (elapsed - this.blinkStartedAt) / blinkDuration;
        if (progress >= 1) {
          blendShape.setValue(VRMSchema.BlendShapePresetName.Blink, 0);
          this.blinkStartedAt = null;
          this.nextBlinkAt = elapsed + 2.5 + Math.random() * 4;
        } else {
          blendShape.setValue(VRMSchema.BlendShapePresetName.Blink, Math.sin(progress * Math.PI));
        }
      }
    },
    animate () {
      this.animationFrameId = requestAnimationFrame(this.animate);

      const delta = Math.min(this.clock.getDelta(), 0.1);
      this.updateIdleAnimation(this.clock.elapsedTime);
      if (this.vrm) this.vrm.update(delta);

      this.updateCamera(delta);
      this.camera.lookAt(new THREE.Vector3(0, 0.85, 0));
      this.renderer.render(this.scene, this.camera );
    },
    updateCamera (delta) {
      // Make the parallax noticeable without pushing the character out of frame.
      this.cameraTargetPosition.set(
        this.cameraBasePosition.x - this.cameraPointer.x * 1.4,
        this.cameraBasePosition.y + this.cameraPointer.y * 0.8,
        this.cameraBasePosition.z
      );

      const damping = 1 - Math.exp(-delta * 3.6);
      this.camera.position.lerp(this.cameraTargetPosition, damping);
    },
    onMouseMove (event) {
      this.setCameraPointer(event.clientX, event.clientY);
    },
    onTouch (event) {
      const touch = event.touches[0];
      if (!touch) return;

      this.setCameraPointer(touch.clientX, touch.clientY);
    },
    setCameraPointer (clientX, clientY) {
      this.cameraPointer.set(
        THREE.MathUtils.clamp(clientX / window.innerWidth * 2 - 1, -1, 1),
        THREE.MathUtils.clamp(clientY / window.innerHeight * 2 - 1, -1, 1)
      );
    },
    onMouseLeave () {
      this.cameraPointer.set(0, 0);
    },
    onResize () {
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
    },
    updateTheme () {
      const surface = getComputedStyle(document.documentElement)
        .getPropertyValue('--color-surface')
        .trim() || '#f2f2f2';
      this.renderer.setClearColor(surface, 1);
    },
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
