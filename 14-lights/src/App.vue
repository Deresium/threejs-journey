<template>
    <canvas :ref="setRef" class="webglCanvas"/>
</template>
<script setup lang="ts">
import {
    AmbientLight, BoxBufferGeometry,
    BoxGeometry, Color, DirectionalLight, HemisphereLight,
    Mesh,
    MeshBasicMaterial, MeshStandardMaterial,
    PerspectiveCamera, PlaneBufferGeometry,
    PointLight, RectAreaLight,
    Scene, SphereBufferGeometry, SpotLight, SpotLightHelper, TorusBufferGeometry,
    WebGLRenderer
} from "three";
import {onMounted, ref} from "vue";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";
import {RectAreaLightHelper} from "three/examples/jsm/helpers/RectAreaLightHelper";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};
onMounted(() => {
    const scene = new Scene();

    // lights
    /*const ambientLight = new AmbientLight(0xffffff, 0.5);
    ambientLight.color = new Color('white');
    scene.add(ambientLight);*/

    /*const pointLight = new PointLight(0xff00ff, 0.5, 2);
    pointLight.position.set(1, -0.5, 1);
    scene.add(pointLight);*/

    // like the sun. Parallels light lines
    /*const directionalLight = new DirectionalLight(0x00ff00, 0.3);
    directionalLight.position.set(1, 0.25, 0);
    scene.add(directionalLight);*/

    /*const hemisphereLight = new HemisphereLight(0x00ff00, 0x0000ff, 0.3);
    scene.add(hemisphereLight);*/

    // like a rectangle photoshoot
    const rectAreaLight = new RectAreaLight(0xff00ff, 2, 1, 1);
    scene.add(rectAreaLight);

    /*const spotLight = new SpotLight(0xff0ff, 0.5, 10, Math.PI * 0.1, 0.25, 1);
    spotLight.position.set(0, 2, 3);
    scene.add(spotLight);

    spotLight.target.position.x = 2;
    scene.add(spotLight.target);*/

    /*const spotlightHelper = new SpotLightHelper(spotLight, 0xffffff);
    window.requestAnimationFrame(() => {
        spotlightHelper.update();
    });
    scene.add(spotlightHelper);*/

    const rectAreaLightHelper = new RectAreaLightHelper(rectAreaLight, 0xff0000);
    scene.add(rectAreaLightHelper);


    // Objects
    const material = new MeshStandardMaterial();
    material.roughness = 0.4;

    const sphere = new Mesh(new SphereBufferGeometry(0.5, 32, 32), material);
    sphere.position.x = -1.5;

    const cube = new Mesh(new BoxBufferGeometry(0.75, 0.75, 0.75), material);

    const torus = new Mesh(new TorusBufferGeometry(0.3, 0.2, 32, 64), material);
    torus.position.x = 1.5;

    const plane = new Mesh(new PlaneBufferGeometry(5, 5), material);
    plane.rotation.x = -Math.PI * 0.5;
    plane.position.y = - 0.65;

    scene.add(sphere, cube, torus, plane);


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

    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

    const tick = () => {
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