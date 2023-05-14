<template>
    <div class="container is-fluid">
      <canvas class="is-fullwidth" ref="renderCanvas"></canvas>
    </div>
  </template>
  
  <script>
  import { Engine, Scene, ArcRotateCamera, Vector3, Vector2, HemisphericLight, MeshBuilder, Texture, StandardMaterial, Color3, Animation, MorphTargetManager, MorphTarget, GUI } from '@babylonjs/core';


  export default {
    name: 'BabylonScene',
    data() {
      return {
        engine: null,
        scene: null,
      };
    },
    mounted() {
      this.initBabylon();
    },
    beforeUnmount() {
      this.engine.stopRenderLoop();
    },
    methods: {
      initBabylon() {
        // Create Babylon engine and scene
        this.engine = new Engine(this.$refs.renderCanvas, true);
        this.scene = new Scene(this.engine);
  
        // Create camera
        const camera = new ArcRotateCamera("Camera", Math.PI / 2, Math.PI / 2, 2, Vector3.Zero(), this.scene);
        camera.attachControl(this.$refs.renderCanvas, true);
  
        // Create light
        new HemisphericLight("light", new Vector3(0, 1, 0), this.scene);

       

        // Create a material
        const materialRed = new StandardMaterial('material', this.scene);
        materialRed.diffuseColor = new Color3(1, 0, 0); // This will create a red material
       
        
        const materialGreen = new StandardMaterial('material2', this.scene);
        materialGreen.diffuseColor = new Color3(0, 1, 0); // This will create a green material
        

        const materialBlue = new StandardMaterial('material3', this.scene);
        materialBlue.diffuseColor = new Color3(0, 0, 1); // This will create a blue material
      

        // Create a texture and assign it to the material
        const texture = new Texture('/texture/mauro-texture.png', this.scene);
        materialRed.diffuseTexture = texture;

        const texture2 = new Texture('/texture/mauro-texture2.png', this.scene);
        materialGreen.diffuseTexture = texture2;

        const texture3 = new Texture('/texture/mauro-texture3.png', this.scene);
        materialBlue.diffuseTexture = texture3;

        // ANIMATIONTYPE_FLOAT: animazione della visibilità della mesh box
        const boxVisibilityAnimation = new Animation('boxVisibilityAnimation', 'visibility', 30, Animation.ANIMATIONTYPE_FLOAT, Animation.ANIMATIONLOOPMODE_CYCLE);
        const boxVisibilityKeys = [{ frame: 0, value: 1 }, { frame: 100, value: 0 }, { frame: 200, value: 1 }];
        boxVisibilityAnimation.setKeys(boxVisibilityKeys);

        // ANIMATIONTYPE_VECTOR3: animazione della posizione della mesh sphere
        const spherePositionAnimation = new Animation('spherePositionAnimation', 'position', 30, Animation.ANIMATIONTYPE_VECTOR3, Animation.ANIMATIONLOOPMODE_CYCLE);
        const spherePositionKeys = [{ frame: 0, value: new Vector3(-3, 0, 0) }, { frame: 100, value: new Vector3(-3, 5, 0) }, { frame: 200, value: new Vector3(-3, 0, 0) }];
        spherePositionAnimation.setKeys(spherePositionKeys);



        // Create geometries
        const box = MeshBuilder.CreateBox('box', { size: 1 }, this.scene); 
        box.position = new Vector3(-5, 0, 0);

        const sphere = MeshBuilder.CreateSphere('sphere', { diameter: 1 }, this.scene);
        sphere.position = new Vector3(-3, 0, 0);

        const cylinder = MeshBuilder.CreateCylinder('cylinder', { height: 1, diameter: 1 }, this.scene);
        cylinder.position = new Vector3(-1, 0, 0);

        const cone = MeshBuilder.CreateCylinder('cone', { height: 1, diameterTop: 0, diameterBottom: 1 }, this.scene);
        cone.position = new Vector3(1, 0, 0);

        const torus = MeshBuilder.CreateTorus('torus', { diameter: 1, thickness: 0.2 }, this.scene);
        torus.position = new Vector3(3, 0, 0);

        const plane = MeshBuilder.CreatePlane('plane', { size: 1 }, this.scene);
        plane.position = new Vector3(-4, 2, 0);

        const ground = MeshBuilder.CreateGround('ground', { width: 1, height: 1 }, this.scene);
        ground.position = new Vector3(-2, 2, 0);

        const disc = MeshBuilder.CreateDisc('disc', { radius: 0.5, tessellation: 48 }, this.scene);
        disc.position = new Vector3(0, 2, 0);

        const icoSphere = MeshBuilder.CreateIcoSphere('icoSphere', { radius: 0.5, subdivisions: 1 }, this.scene);
        icoSphere.position = new Vector3(2, 2, 0);
        
        //Cambiare il type per ottenere altri tipi di poliedri
        const polyhedron = MeshBuilder.CreatePolyhedron('polyhedron', { type: 1, size: 0.5 }, this.scene);
        polyhedron.position = new Vector3(4, 2, 0);

        //Il decal è un'immagine o una texture che viene proiettata su un'altra mesh
        const decal = MeshBuilder.CreateDecal('decal', box, { size: new Vector3(0.5, 0.5, 0.5) });
        decal.position = new Vector3(-3, 2, 0);

        const torusKnot = MeshBuilder.CreateTorusKnot('torusKnot', { radius: 0.5, tube: 0.2 }, this.scene);
        torusKnot.position = new Vector3(-1, 2, 0);

        //Questi metodi richiedono un array di oggetti Vector3 per definire il percorso del tubo o delle linee.
        const tube = MeshBuilder.CreateTube('tube', { path: [new Vector3(1, 2, 0), new Vector3(2, 2, 0)], radius: 0.2 }, this.scene);

        const lines = MeshBuilder.CreateLines('lines', { points: [new Vector3(3, 2, 0), new Vector3(4, 2, 0)] }, this.scene);

        const dashedLines = MeshBuilder.CreateDashedLines('dashedLines', { points: [new Vector3(-4, 4, 0), new Vector3(-3, 4, 0)] }, this.scene);

        //stiamo creando una forma estrusa che è come una forma 2D che è stata "tirata" in una terza dimensione per creare un solido. Questo metodo richiede due array di oggetti Vector3: uno per definire la forma da estrudere e uno per definire il percorso lungo il quale estrudere la forma.
        const extrusionShape = MeshBuilder.ExtrudeShape('extrusion', { shape: [new Vector3(-2, 4, 0), new Vector3(-1, 4, 0)], path: [new Vector3(-2, 4, 0), new Vector3(-1, 4, 0), new Vector3(-1, 4.5, 0)] }, this.scene);

        //Questo metodo richiede un array di array di oggetti Vector3 per definire i percorsi del nastro.
        const ribbon = MeshBuilder.CreateRibbon('ribbon', { pathArray: [[new Vector3(1, 4, 0), new Vector3(2, 4, 0)], [new Vector3(1, 4.5, 0), new Vector3(2, 4.5, 0)]] }, this.scene);

        
        const lathe = MeshBuilder.CreateLathe('lathe', { shape: [new Vector3(3, 4, 0), new Vector3(4, 4, 0)], radius: 0.5 }, this.scene);

        const tiledGround = MeshBuilder.CreateTiledGround('tiledGround', { xmin: -5, zmin: -5, xmax: 5, zmax: 5, subdivisions: { w: 1, h: 1 }, precision: { w: 1, h: 1 } }, this.scene);
        tiledGround.position.y = -1;

        const lineSystem = MeshBuilder.CreateLineSystem('lineSystem', { lines: [[new Vector3(-4, 5, 0), new Vector3(-3, 5, 0)], [new Vector3(-2, 5, 0), new Vector3(-1, 5, 0)]] }, this.scene);
        


        // Apply the material to the meshes
        sphere.material = materialRed;
        box.material = materialGreen;
        plane.material = materialBlue;
        disc.material = materialRed;
        icoSphere.material =materialGreen;
        cylinder.material = materialBlue;
        torus.material = materialRed;
        ribbon.material = materialGreen;
        polyhedron.material = materialBlue;
        decal.material = materialRed;
        torusKnot.material = materialGreen;
        tube.material = materialBlue;
        lines.material = materialRed;
        dashedLines.material = materialGreen;
        extrusionShape.material = materialBlue;
        lathe.material = materialRed;
        tiledGround.material = materialGreen;
        lineSystem.material = materialBlue;
        cone.material = materialRed;
        ground.material = materialBlue;

        materialRed.wireframe = false
        materialGreen.wireframe = false
        materialBlue.wireframe = false

        //assegno le animazioni
        box.animations.push(boxVisibilityAnimation);
        sphere.animations.push(spherePositionAnimation);


        this.scene.beginAnimation(box, 0, 200, true);
        this.scene.beginAnimation(sphere, 0, 200, true);


        // Start render loop
        this.engine.runRenderLoop(() => {
          if (this.scene) {
            this.scene.render();
          }
        });
  
        // Resize the engine if window size changes
        window.addEventListener('resize', () => {
          this.engine.resize();
        });
      },
    },
  };
  </script>
  
<style scoped>
  canvas {
    width: 100%;
    height: 100%;
  }
</style>