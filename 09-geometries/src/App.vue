<template>
    <canvas :ref="setRef"/>
</template>

<script setup lang="ts">
import {onMounted, ref} from "vue";
import {BufferAttribute, BufferGeometry, Mesh, MeshBasicMaterial, PerspectiveCamera, Scene, WebGLRenderer} from "three";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};

onMounted(() => {
    const scene = new Scene();

    const count = 50;
    const positionsArray = new Float32Array(count * 3 * 3);

    for(let i = 0; i < count * 3 * 3; ++i){
        positionsArray[i] = Math.random();
    }

    const positionsAttributes = new BufferAttribute(positionsArray, 3);

    const geometry = new BufferGeometry();
    geometry.setAttribute('position', positionsAttributes);

    const material = new MeshBasicMaterial({
        color: 0xff0000,
        wireframe: true
    });

    const mesh = new Mesh(geometry, material);

    scene.add(mesh);

    // Sizes
    const sizes = {
        width: 800,
        height: 600
    };

    // camera
    const camera = new PerspectiveCamera(75, sizes.width / sizes.height);
    camera.position.z = 3;
    scene.add(camera);

    // renderer
    const renderer = new WebGLRenderer({
        canvas: webgl.value
    });

    renderer.setSize(sizes.width, sizes.height);

    renderer.render(scene, camera);
});

</script>

<style>
</style>
