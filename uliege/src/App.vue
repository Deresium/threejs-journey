<template>
    <canvas class="webglCanvas" :ref="setRef"/>
</template>
<script setup lang="ts">
import {
    AmbientLight,
    BoxGeometry, Group, HemisphereLight,
    Mesh,
    MeshBasicMaterial,
    PerspectiveCamera,
    PointLight,
    Scene,
    WebGLRenderer
} from "three";
import {onMounted, ref} from "vue";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";
import {GLTFLoader} from "three/examples/jsm/loaders/GLTFLoader";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};
onMounted(() => {
    const scene = new Scene();

    const loader = new GLTFLoader();
    let uliege: Group | null = null;
    loader.load('/uliege2.glb', (gltf) => {
        uliege = gltf.scene;
        scene.add(uliege);
    }, undefined, error => {
        console.log(error);
    });

    const geometry = new BoxGeometry(1, 1, 1);
    const material = new MeshBasicMaterial({
        color: '#f00'
    });
    const mesh = new Mesh(geometry, material);

    //scene.add(mesh);

    // Sizes
    const sizes = {
        width: 800,
        height: 600
    };

    // camera
    const camera = new PerspectiveCamera(75, sizes.width / sizes.height);
    camera.position.z = 10;
    scene.add(camera);

    // controls
    const controls = new OrbitControls(camera, webgl.value);
    controls.enableDamping = true;

    // light
    const light = new PointLight();
    light.position.z = 1000;
    light.power = 20;
    camera.add(light);


    // renderer
    const renderer = new WebGLRenderer({
        canvas: webgl.value
    });

    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.setClearColor(0xffffff);

    const tick = () => {
        controls.update();
        renderer.render(scene, camera);
        //camera.lookAt(uliege);
        window.requestAnimationFrame(tick);
    };
    tick();
});

</script>
<style>
.webglCanvas{

}

</style>
