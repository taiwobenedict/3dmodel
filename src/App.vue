<template>
  <div class="app">
    <canvas id="canvas" ref="car3d"></canvas>
  </div>
</template>

<script>
import * as THREE from "three";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader";
import { OrbitControls } from "three/addons/controls/OrbitControls";

export default {
  mounted() {
    this.init();
    this.renderScene();
    this.addEventListeners();
    this.animate();
  },
  data() {
    return {
      rotationAngle: 0,
      carModel: null,
      mouse: new THREE.Vector2(),
      previousMousePosition: new THREE.Vector2(),
      isMouseDown: false,
    };
  },
  methods: {
    init() {
      this.canvas = document.querySelector("#canvas");
      this.renderer = new THREE.WebGLRenderer({ canvas: this.canvas });
      this.renderer.gammaOutput = true;
      this.renderer.shadowMap.enabled = true;
      this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

      const sizes = {
        width: window.innerWidth,
        height: window.innerHeight,
      };
      this.renderer.setSize(sizes.width, sizes.height);

      this.scene = new THREE.Scene();
      this.scene.background = new THREE.Color(0xdddddd);

      this.camera = new THREE.PerspectiveCamera(50, sizes.width / sizes.height, 1, 1000);
      this.camera.rotation.y = (45 / 180) * Math.PI;

      this.camera.position.set(0.609166, 0.70933, -0.21579);
      this.camera.lookAt(0, 0, 0);

      const loader = new GLTFLoader();
      loader.load("./assets/car_model.glb", (gltf) => {
        const car_model = gltf.scene;

        const boundingBox = new THREE.Box3().setFromObject(car_model);
        const boundingBoxCenter = boundingBox.getCenter(new THREE.Vector3());

        car_model.position.sub(boundingBoxCenter);
        car_model.scale.set(5, 5, 5);
        car_model.position.add(boundingBoxCenter);

        this.carModel = car_model;
        this.scene.add(car_model);
      });

      // Orbit Control
      const controls = new OrbitControls(this.camera, this.renderer.domElement);

      //controls.update() must be called after any manual changes to the camera's transform
      this.camera.position.set(0, 20, 100);
      controls.update();

      this.hlight = new THREE.AmbientLight(0x404040, 100);
      this.scene.add(this.hlight);

      this.directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      this.directionalLight.position.set(0, 1, 0);
      this.directionalLight.castShadow = true;
      this.scene.add(this.directionalLight);

      this.light = new THREE.PointLight(0xc4c4c4, 10);
      this.light.position.set(0, 300, 500);
      this.scene.add(this.light);

      this.light2 = new THREE.PointLight(0xc4c4c4, 10);
      this.light2.position.set(500, 100, 0);
      this.scene.add(this.light2);

      this.light3 = new THREE.PointLight(0xc4c4c4, 10);
      this.light3.position.set(0, 100, -500);
      this.scene.add(this.light3);

      this.light4 = new THREE.PointLight(0xc4c4c4, 10);
      this.light4.position.set(-500, 300, 500);
      this.scene.add(this.light4);
    },
    animate() {
      if (this.isMouseDown) {
        const deltaX = this.mouse.x - this.previousMousePosition.x;
        this.rotationAngle -= deltaX * 0.005;
      }

      if (this.carModel) {
        const car_model = this.carModel;
        const boundingBox = new THREE.Box3().setFromObject(car_model);
        const boundingBoxCenter = boundingBox.getCenter(new THREE.Vector3());

        car_model.position.sub(boundingBoxCenter);
        car_model.rotation.y = this.rotationAngle;
        car_model.position.add(boundingBoxCenter);
      }

      
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    },
    changeCarColor(colorCode) {
      if (this.carModel) {
        const newColor = new THREE.Color(
          ("#" + colorCode).replace(/(?!^)#+/g, "")
        );
        this.carModel.traverse((child) => {
          if (child.isMesh) {
            const meshMaterial = child.material;
            if (meshMaterial.color.r > 0.2) {
              meshMaterial.color.copy(newColor);
            }
          }
        });
      }
    },
    renderScene() {
      this.renderer.render(this.scene, this.camera);
    },
    addEventListeners() {
      this.canvas.addEventListener("mousemove", this.onMouseMove);
      this.canvas.addEventListener("mousedown", this.onMouseDown);
      this.canvas.addEventListener("mouseup", this.onMouseUp);
      this.canvas.addEventListener("mouseleave", this.onMouseLeave);
      this.canvas.addEventListener("wheel", this.onWheel);
      window.addEventListener("resize", this.onWindowResize);
    },
    onMouseMove(event) {
      this.previousMousePosition.copy(this.mouse);
      this.mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      this.mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    },
    onMouseDown(event) {
      this.isMouseDown = true;
      this.previousMousePosition.copy(this.mouse);
      event.preventDefault();
    },
    onMouseUp() {
      this.isMouseDown = false;
    },
    onMouseLeave() {
      this.isMouseDown = false;
    },
    onWheel(event) {
      this.camera.position.z -= event.deltaY * 0.01;
    },
    onWindowResize() {
      const sizes = {
        width: window.innerWidth,
        height: window.innerHeight,
      };

      this.camera.aspect = sizes.width / sizes.height;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(sizes.width, sizes.height);
    },
  },
};
</script>

<style>
.app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  height: 100% !important;
  width: 100% !important;
}

.position-header-right {
  position: absolute;
  right: 20px;
  top: 15px;
  max-width: 222px;
}

a,
button {
  color: #4fc08d;
}

button {
  background: none;
  border: solid 1px;
  border-radius: 2em;
  font: inherit;
  padding: 0.75em 2em;
}

#canvas {
  width: 100%;
  height: 100%;
}
</style>
