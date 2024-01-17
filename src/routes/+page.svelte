<script>
    import * as THREE from 'three';
    import { onMount } from 'svelte';
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

    let camera, scene, renderer;

    onMount(async () => {
        scene = new THREE.Scene();
        scene.background = new THREE.TextureLoader().load( "https://www.imagesdoc.com/wp-content/uploads/sites/33/2019/12/AdobeStock_298012912-1024x475.jpeg" );

        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(0, 20, 100);

        renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        document.body.appendChild(renderer.domElement);
        document.getElementById('three-container').appendChild(renderer.domElement);

        const loader = new GLTFLoader();
        loader.load(
            'goutte_eau.gltf',
            function (gltf) {
                gltf.scene.traverse(function (object) {
                    if (object.isMesh) {
                        object.castShadow = true;
                        object.receiveShadow = true;
                    }
                });
                scene.add(gltf.scene);
            },
            undefined,
            function (error) {
                console.error(error);
            }
        );

        const light = new THREE.PointLight(0xffffff, 1);
        light.position.set(3, 3, 3);
        light.castShadow = true;
        scene.add(light);

        light.shadow.bias = -0.002;
        light.shadow.radius = 4;

        const controls = new OrbitControls(camera, renderer.domElement);
        controls.update();

        function animate() {
            requestAnimationFrame(animate);
            controls.update();
            renderer.render(scene, camera);
        }

        animate();
    });
</script>

<div id="three-container" class="">
    <div class="bg-[url(https://blog.cobrason.com/wp-content/uploads/2017/02/Salle_de_Cinéma_1024p.jpg)] bg-cover bg-no-repeat h-screen w-screen absolute -z-10">
    </div>
</div>

<style>
    :global(#three-container canvas) {
        position: absolute;
        inset: 0;
    }
</style>
