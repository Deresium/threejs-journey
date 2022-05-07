<template>
    <canvas :ref="setRef"/>
</template>
<script setup lang="ts">
import {AxesHelper, BoxGeometry, Mesh, MeshBasicMaterial, PerspectiveCamera, Scene, WebGLRenderer, Group} from "three";
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
    mesh.position.x = 0.7;
    mesh.position.y = -0.6;
    mesh.position.z = 1;

    mesh.scale.y = 4;

    //mesh.rotation.reorder('yxz');
    mesh.rotation.z = Math.PI/4;


    console.log('distance between cube and center of the scene', mesh.position.length());
    // back to length = 1
    mesh.position.normalize();
    console.log('normalize length', mesh.position.length());
    mesh.position.set(0.7, -0.6, 1);
    //scene.add(mesh);


    // group
    const group = new Group();
    const redCube = new Mesh(new BoxGeometry(1, 1, 1), new MeshBasicMaterial({color: '#f00'}));
    const blueCube = new Mesh(new BoxGeometry(1, 1, 1), new MeshBasicMaterial({color: '#00f'}));
    const greenCube = new Mesh(new BoxGeometry(1, 1, 1), new MeshBasicMaterial({color: '#0f0'}));

    redCube.position.x = 4;
    blueCube.position.x = -4;

    group.add(redCube);
    group.add(blueCube);
    group.add(greenCube);

    group.rotation.z = Math.PI/4;

    scene.add(group);


    // Sizes
    const sizes = {
        width: 800,
        height: 600
    };

    // camera
    const camera = new PerspectiveCamera(75, sizes.width / sizes.height);
    camera.position.z = 10;
    camera.lookAt(mesh.position);
    scene.add(camera);

    console.log('distance bewteen cube and camera', mesh.position.distanceTo(camera.position));

    // axes helper
    const axesHelper = new AxesHelper(3);
    scene.add(axesHelper);

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