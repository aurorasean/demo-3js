<script lang="ts">
    import * as THREE from "three";
    import * as SC from "svelte-cubed";

    import { GLTFLoader, type GLTF } from "three/addons/loaders/GLTFLoader.js";

    let model: GLTF;
    let mixer: any;
    let clock = new THREE.Clock();

    const loader = new GLTFLoader();
    loader.load("./models/scene.glb", (gltf) => {
        model = gltf;
        const scene = model.scene;
        // scene.scale.set(0.01, 0.01, 0.01);
        scene.position.set(0, 0, 0);
        scene.rotation.set(0, 0, 0);
        scene.traverse((child: any) => {
            if (child.isMesh) {
                child.castShadow = true;
                child.receiveShadow = true;
            }
        });
        mixer = new THREE.AnimationMixer(scene);
        gltf.animations.forEach((clip) => {
            mixer.clipAction(clip).play();
        });
        console.log({ gltf, model });
    });

    function animate() {
        // requestAnimationFrame(animate);
        if (mixer) {
            mixer.update(clock.getDelta());
        }
    }
    animate();
</script>

<SC.Canvas antialias background={new THREE.Color("white")} stencil>
    {#if model}
        <SC.Primitive object={model.scene} />
    {/if}

    <SC.PerspectiveCamera position={[1, 1, 3]} />
    <SC.OrbitControls
        enablePan={false}
        maxPolarAngle={1.6}
        minPolarAngle={1}
        dampingFactor={0.1}
        enableDamping={true}
    />
    <SC.AmbientLight intensity={1} />
    <SC.DirectionalLight intensity={2} position={[-2, 3, 2]} />
</SC.Canvas>
