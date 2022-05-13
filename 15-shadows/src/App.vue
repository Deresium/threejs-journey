<template>
    <canvas :ref="setRef" class="webglCanvas"/>
</template>
<script setup lang="ts">
import {
    AmbientLight,
    BoxGeometry, CameraHelper, Clock, DirectionalLight,
    Mesh,
    MeshBasicMaterial,
    MeshStandardMaterial, PCFSoftShadowMap,
    PerspectiveCamera, PlaneBufferGeometry, PointLight,
    Scene, SphereBufferGeometry, SpotLight, TextureLoader,
    WebGLRenderer
} from "three";
import {onMounted, ref} from "vue";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};
onMounted(() => {
    const scene = new Scene();

    const textureLoader = new TextureLoader();
    const bakedShadow = textureLoader.load('/bakedShadow.jpg');
    const simpleShadow = textureLoader.load('/simpleShadow.jpg');

    const material = new MeshStandardMaterial();
    material.roughness = 0.4;

    const plane = new Mesh(new PlaneBufferGeometry(5, 5), material);
    plane.rotation.x = -Math.PI * 0.5;
    plane.position.y = - 0.65;
    plane.receiveShadow = true;

    const sphereShadow = new Mesh(
        new PlaneBufferGeometry(1.5, 1.5),
        new MeshBasicMaterial({
            color: 'black',
            transparent: true,
            alphaMap: simpleShadow
        })
    );

    sphereShadow.rotation.x = - Math.PI / 2;
    sphereShadow.position.y = plane.position.y + 0.01;
    scene.add(sphereShadow);

    const sphere = new Mesh(new SphereBufferGeometry(0.5, 32, 32), material);
    sphere.castShadow = true;

    scene.add(sphere, plane);

    const directionalLight = new DirectionalLight(0xff00ff, 0.5);
    directionalLight.position.set(2, 2, -1);
    directionalLight.castShadow = true;
    directionalLight.shadow.mapSize.width = 1024;
    directionalLight.shadow.mapSize.height = 1024;
    directionalLight.shadow.camera.near = 1;
    directionalLight.shadow.camera.far = 6;
    directionalLight.shadow.camera.top = 2;
    directionalLight.shadow.camera.right = 2;
    directionalLight.shadow.camera.bottom = -2;
    directionalLight.shadow.camera.left = -2;
    //directionalLight.shadow.radius = 10;
    //scene.add(directionalLight);

    const directionalLightCameraHelper = new CameraHelper(directionalLight.shadow.camera);
    //scene.add(directionalLightCameraHelper);

    const spotLight = new SpotLight(0xff00ff, 0.4, 10, Math.PI * 0.3);
    spotLight.castShadow = true;
    spotLight.position.set(0, 2, 2);
    spotLight.shadow.mapSize.width = 1024;
    spotLight.shadow.mapSize.height = 1024;
    spotLight.shadow.camera.fov= 30;
    spotLight.shadow.camera.far = 1;
    spotLight.shadow.camera.far = 6;
    //scene.add(spotLight);
    //scene.add(spotLight.target);

    const spotLightCameraHelper = new CameraHelper(spotLight.shadow.camera);
    //scene.add(spotLightCameraHelper);

    const pointLight = new PointLight(0xff00ff, 0.3);
    pointLight.castShadow = true;
    pointLight.position.set(-1, 1, 0);
    pointLight.shadow.mapSize.width = 1024;
    pointLight.shadow.mapSize.height = 1024;
    pointLight.shadow.camera.far = 0.1;
    pointLight.shadow.camera.far = 5;
    scene.add(pointLight);

    const pointLightCameraHelper = new CameraHelper(pointLight.shadow.camera);
    //scene.add(pointLightCameraHelper);

    /*const ambientLight = new AmbientLight(0xffffff, 0.1);
    scene.add(ambientLight);*/


    // Sizes
    const sizes = {
        width: window.innerWidth,
        height: window.innerHeight
    };

    // camera
    const camera = new PerspectiveCamera(75, sizes.width / sizes.height);
    camera.position.z = 3;
    scene.add(camera);

    // controls
    const controls = new OrbitControls(camera, webgl.value);
    controls.enableDamping = true;

    // renderer
    const renderer = new WebGLRenderer({
        canvas: webgl.value
    });

    //renderer.shadowMap.enabled = true;

    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.shadowMap.type = PCFSoftShadowMap;

    const clock = new Clock();

    const tick = () => {
        // animation sphere
        sphere.position.x = Math.cos(clock.getElapsedTime()) * 1.5;
        sphere.position.z = Math.sin(clock.getElapsedTime()) * 1.5;
        sphere.position.y = Math.abs(Math.sin(clock.getElapsedTime()*3));

        // update shadow
        sphereShadow.position.x = sphere.position.x;
        sphereShadow.position.z = sphere.position.z;
        sphere.material.opacity = (1 - sphere.position.y) * 0.3;

        controls.update();
        renderer.render(scene, camera);
        window.requestAnimationFrame(tick);
    };
    tick();

    window.addEventListener('resize', event => {
        sizes.width = window.innerWidth;
        sizes.height = window.innerHeight;

        camera.aspect = sizes.width / sizes.height;
        camera.updateProjectionMatrix();

        renderer.setSize(sizes.width, sizes.height);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    });

    window.addEventListener('dblclick', event => {
        if (document.fullscreenElement) {
            document.exitFullscreen();
        } else {
            webgl.value.requestFullscreen();
        }
    });
});

</script>
<style>
.webglCanvas{
    margin: 0;
    padding: 0;
    position: fixed;
    top: 0;
    left: 0;
    outline: none;
}

</style>
