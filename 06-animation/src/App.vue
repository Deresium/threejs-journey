<template>
    <canvas :ref="setRef"/>
</template>
<script setup lang="ts">
import {
    BoxGeometry,
    Clock,
    Mesh,
    MeshBasicMaterial,
    OrthographicCamera,
    PerspectiveCamera,
    Scene,
    WebGLRenderer
} from "three";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";
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

    const cursor = {
        x: 0,
        y: 0
    };

    window.addEventListener('mousemove', (event) => {
        cursor.x = event.clientX / sizes.width - 0.5;
        cursor.y = -(event.clientY / sizes.height - 0.5);
    });

    // perspective camera
    const camera = new PerspectiveCamera(75, sizes.width / sizes.height);
    camera.position.z = 3;
    scene.add(camera);

    // orbit controls
    const controls = new OrbitControls(camera, webgl.value);
    controls.enableDamping = true;

    // ortographic camera
    const aspectRatio = sizes.width / sizes.height;
    const ortoCamera = new OrthographicCamera(-1 * aspectRatio, aspectRatio, 1, -1, 0.1, 100);
    ortoCamera.position.z = 3;

    // renderer
    const renderer = new WebGLRenderer({
        canvas: webgl.value
    });

    renderer.setSize(sizes.width, sizes.height);

    const clock = new Clock();

    const tick = () => {
        const ellapseTime = clock.getElapsedTime();
        console.log(ellapseTime*Math.PI);
        //mesh.rotation.y = ellapseTime * Math.PI;
        /*camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 3;
        camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 3;
        camera.position.y = cursor.y * 10;
        camera.lookAt(mesh.position);*/

        controls.update();
        renderer.render(scene, camera);
        window.requestAnimationFrame(tick);
    };
    tick();
});

</script>
<style>

</style>