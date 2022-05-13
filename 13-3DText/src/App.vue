<template>
    <canvas :ref="setRef" class="webglCanvas"/>
</template>
<script setup lang="ts">
import {
    AxesHelper,
    BoxGeometry,
    Mesh,
    MeshBasicMaterial, MeshMatcapMaterial,
    PerspectiveCamera,
    Scene,
    TextureLoader, TorusBufferGeometry,
    WebGLRenderer
} from "three";
import {onMounted, ref} from "vue";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";
import {FontLoader} from "three/examples/jsm/loaders/FontLoader";
import {TextGeometry} from "three/examples/jsm/geometries/TextGeometry";

const webgl = ref();

const setRef = (el: any) => {
    webgl.value = el;
};
onMounted(() => {
    const scene = new Scene();

    const axesHelper = new AxesHelper();
    scene.add(axesHelper);

    const textureLoader = new TextureLoader();
    const matcapTexture = textureLoader.load('/1.png');

    const material = new MeshMatcapMaterial({matcap: matcapTexture});


    const fontLoader = new FontLoader();
    fontLoader.load('/helvetiker_regular.typeface.json', (font) => {
        const textGeometry = new TextGeometry(
            'Creatiview',
            {
                font,
                size: 0.5,
                height: 0.2,
                curveSegments: 5,
                bevelEnabled: true,
                bevelThickness: 0.03,
                bevelSize: 0.02,
                bevelOffset: 0,
                bevelSegments: 4
            }
        );

        textGeometry.computeBoundingBox();
        /*if(textGeometry.boundingBox) {
            textGeometry.translate(
                (-textGeometry.boundingBox.max.x - 0.02) * 0.5,
                (-textGeometry.boundingBox.max.y - 0.02) * 0.5,
                (-textGeometry.boundingBox.max.z - 0.03) * 0.5
            );
        }*/
        textGeometry.center();

        const textMesh = new Mesh(textGeometry, material);
        scene.add(textMesh);

    }, (event) => {

    }, (event) => {
        console.log(event);
    });

    // add donuts

    const donutGeometry = new TorusBufferGeometry(0.3, 0.2, 20, 45);

    for(let i = 0; i < 200; ++i) {
        const donutMesh = new Mesh(donutGeometry, material);

        donutMesh.position.x = (Math.random() - 0.5) * 10;
        donutMesh.position.y = (Math.random() - 0.5) * 10;
        donutMesh.position.z = (Math.random() - 0.5) * 10;

        donutMesh.rotation.x = Math.random() * Math.PI;
        donutMesh.rotation.y = Math.random() * Math.PI;

        const scale = Math.random();
        donutMesh.scale.set(scale, scale, scale);
        scene.add(donutMesh);
    }


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
.webglCanvas {
    margin: 0;
    padding: 0;
    position: fixed;
    top: 0;
    left: 0;
    outline: none;
}

</style>
