<template>
    <canvas :ref="setRef" class="webglCanvas"/>
</template>
<script setup lang="ts">
import {
    AmbientLight,
    BoxGeometry, BufferAttribute,
    Clock, Color, CubeTextureLoader,
    DoubleSide,
    FrontSide,
    Mesh,
    MeshBasicMaterial,
    MeshDepthMaterial,
    MeshLambertMaterial,
    MeshMatcapMaterial,
    MeshNormalMaterial,
    MeshPhongMaterial, MeshStandardMaterial, MeshToonMaterial, NearestFilter,
    PerspectiveCamera,
    PlaneBufferGeometry,
    PointLight,
    Scene,
    SphereBufferGeometry,
    TextureLoader,
    TorusBufferGeometry,
    WebGLRenderer
} from "three";
import {onMounted, ref} from "vue";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};
onMounted(() => {
    // Textures
    const textureLoader = new TextureLoader();
    const cubeTextureLoader = new CubeTextureLoader();

    const doorColorTexture = textureLoader.load('/albedo_texture.jpg');
    const alphaTexture = textureLoader.load('/alpha_texture.jpg');
    const ambientOcclusionTexture = textureLoader.load('/ambient_occlusion_texture.jpg');
    const heightTexture = textureLoader.load('/height_texture.png');
    const metalnessTexture = textureLoader.load('/metalness_texture.jpg');
    const normalTexture = textureLoader.load('/normal_texture.jpg');
    const roughnessTexture = textureLoader.load('/roughness_texture.jpg');
    const matcapTexture = textureLoader.load('/1.png');
    const gradientTexture = textureLoader.load('/3.jpg');

    const environmentMapTexture = cubeTextureLoader.load([
        '/environmentmap/px.jpg',
        '/environmentmap/nx.jpg',
        '/environmentmap/py.jpg',
        '/environmentmap/ny.jpg',
        '/environmentmap/pz.jpg',
        '/environmentmap/nz.jpg',
    ]);

    const scene = new Scene();

    /*const material = new MeshBasicMaterial();
    material.map = doorColorTexture;

    //material.opacity = 0.5;
    material.transparent = true;
    material.alphaMap = alphaTexture;
    material.side = DoubleSide;*/

    // normal material are arrays that points the direction (for reflection, light, ... )
    /*const material = new MeshNormalMaterial();
    material.flatShading = true;*/

    /*const material = new MeshMatcapMaterial();
    material.matcap = matcapTexture;*/

    /*const material = new MeshDepthMaterial();*/

    /*const material = new MeshLambertMaterial();*/

    /*const material = new MeshPhongMaterial();
    material.shininess = 100;
    material.specular = new Color('blue');*/

    /*const material = new MeshToonMaterial();
    material.gradientMap = gradientTexture;
    gradientTexture.magFilter = NearestFilter;
    gradientTexture.generateMipmaps = false;*/

    /*const material = new MeshStandardMaterial();
    /!*material.metalness =  0.45;
    material.roughness = 0.45;*!/
    material.map = doorColorTexture;
    material.aoMap = ambientOcclusionTexture;
    material.aoMapIntensity = 2;
    material.displacementMap = heightTexture;
    material.displacementScale = 0.05;
    material.metalnessMap = metalnessTexture;
    material.roughnessMap = roughnessTexture;
    material.normalMap = normalTexture;
    material.normalScale.set(0.5, 0.5);
    material.transparent = true;
    material.alphaMap = alphaTexture;*/

    const material = new MeshStandardMaterial();
    material.metalness = 0.7;
    material.roughness = 0.2;
    material.envMap = environmentMapTexture;



    const ambientLight = new AmbientLight(0xffffff, 0.5);
    scene.add(ambientLight);

    const pointLight = new PointLight(0xffffff, 0.5);
    pointLight.position.x = 2;
    pointLight.position.y = 3;
    pointLight.position.z = 4;
    scene.add(pointLight);

    const sphere = new Mesh(new SphereBufferGeometry(0.5, 64, 64), material);
    sphere.position.x = - 1.5;

    const plane = new Mesh(new PlaneBufferGeometry(1, 1, 100, 100), material);
    plane.geometry.setAttribute('uv2', new BufferAttribute(plane.geometry.attributes.uv.array, 2));

    const taurus = new Mesh(new TorusBufferGeometry(0.3, 0.2, 64, 128), material);
    taurus.position.x = 1.5;

    scene.add(sphere, plane, taurus);

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

    const clock = new Clock();
    const tick = () => {
        const elapsedTime = clock.getElapsedTime();

        sphere.rotation.y = 0.1 * elapsedTime;
        plane.rotation.y = 0.1 * elapsedTime;
        taurus.rotation.y = 0.1 * elapsedTime;

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
