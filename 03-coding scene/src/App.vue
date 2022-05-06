<template>
    <canvas :ref="setRef"/>
</template>
<script setup lang="ts">
import {BoxGeometry, Mesh, MeshBasicMaterial, PerspectiveCamera, Scene, WebGLRenderer} from "three";
import {onMounted, ref} from "vue";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};
onMounted(() => {
    const scene = new Scene();

    const geometry = new BoxGeometry(1, 1, 1);
    const material = new MeshBasicMaterial({
        color: '#f00'
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
