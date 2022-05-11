<template>
    <canvas :ref="setRef" class="webglCanvas"></canvas>
</template>

<script setup lang="ts">
import {onMounted, ref} from "vue";
import {
    BoxBufferGeometry, LoadingManager,
    Mesh,
    MeshBasicMaterial, MirroredRepeatWrapping, NearestFilter,
    PerspectiveCamera,
    Scene,
    Texture,
    TextureLoader,
    WebGLRenderer
} from "three";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};

onMounted(() => {
    const scene = new Scene();

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

    // texture load native
   /* const image = new Image();
    image.src = '/albedo_texture.jpg';
    const texture = new Texture(image);
    image.addEventListener('load', () => {
        texture.needsUpdate = true;
    });*/


    // loading manager to manage all loaders
    const loadingManager = new LoadingManager();

    loadingManager.onStart = () => {

    };

    loadingManager.onLoad = () =>{
        console.log('loaded');
    };

    loadingManager.onProgress = (url, loaded, total) => {
        console.log(url, loaded, total);
    };

    loadingManager.onError = (error) => {
        console.error(error);
    };


    //texture load with Three.js
    const textureLoader = new TextureLoader(loadingManager);
    const colorTexture = textureLoader.load('/albedo_texture.jpg');

    /*colorTexture.repeat.x = 2;
    colorTexture.repeat.y = 3;

    colorTexture.wrapS = MirroredRepeatWrapping;

    colorTexture.offset.x = 0.5;*/

    /*colorTexture.center.x = 0.5;
    colorTexture.center.y = 0.5;
    colorTexture.rotation = Math.PI/4;*/

    //mipmappings will generate multiple textures of the same texture
    //it will be a bigger file to store but you will get blurry by far and sharp nearby (manage by threejs)
    // if this effect is not neccesary, just desactivate it
    colorTexture.generateMipmaps = false;

    // when the texture is bigger than the render zone
    colorTexture.minFilter = NearestFilter;

    // when the texture is smaller than the render zone
    colorTexture.magFilter = NearestFilter;

    const alphaTexture = textureLoader.load('/alpha_texture.jpg');
    const ambientOcclusionTexture = textureLoader.load('/ambient_occlusion_texture.jpg');
    const heightTexture = textureLoader.load('/height_texture.png');
    const metalnessTexture = textureLoader.load('/metalness_texture.jpg');
    const normalTexture = textureLoader.load('/normal_texture.jpg');
    const roughnessTexture = textureLoader.load('/roughness_texture.jpg');

    //material
    const geometry = new BoxBufferGeometry(1, 1, 1);
    const material = new MeshBasicMaterial({map: colorTexture});
    const mesh = new Mesh(geometry, material);
    scene.add(mesh);

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

    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
});
</script>

<style>
.webglCanvas {
    margin: 0;
    padding: 0;
    position: fixed;
    top: 0;
    left: 0;
    outline: none;
}
</style>
