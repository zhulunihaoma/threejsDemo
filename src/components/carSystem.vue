<template>

</template>
<script setup>
import * as THREE from 'three';
import Stat from 'three/examples/jsm/libs/stats.module'
// 性能监测面板
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

const w = window.innerWidth;
const h = window.innerHeight;
const stat = new Stat ();
// Scene
const scene = new THREE.Scene();

// 公共的材质
const m = new THREE.MeshNormalMaterial({wireframe:false});
// car 整个汽车group
const car = new THREE.Group();

// 前面两个轮子 frontWheels-group
const frontWheels = new THREE.Group();
// 轮子1 wheel1-group
const wheel1 = new THREE.Group();
const wheelG = new THREE.TorusGeometry(0.5, 0.1, 10, 20);
// 轮子主体的内容
const wheel1Mesh = new THREE.Mesh(wheelG, m);
wheel1.add(wheel1Mesh);


//轮毂的样式
const n = 10;
for (let i = 0; i< n; i ++){
    const g = new THREE.CylinderGeometry(0.03, 0.03, 1);
    const mesh = new THREE.Mesh(g, m);
    mesh.rotation.z = (Math.PI *2 /n) * i; 
    wheel1.add(mesh)
}
// 车轴
const len = 2;
const cylinderG = new THREE.CylinderGeometry(0.05, 0.05, len);
const cylinder = new THREE.Mesh(cylinderG, m);
cylinder.rotation.x = -0.5 * Math.PI
wheel1.position.z = -len/2;
// 轮子2 wheel2 
const wheel2 = wheel1.clone();
wheel2.position.z = len/2;


frontWheels.add(wheel1, cylinder, wheel2);
frontWheels.rotation.y = 0.5 * Math.PI;
frontWheels.position.y = -1;
// 后面的两个轮子 backWheels
const backWheels = frontWheels.clone();
backWheels.position.y = 1;
// 


car.add(frontWheels, backWheels);

scene.add(car);



// Camera
const camera = new THREE.PerspectiveCamera(75, w/h, 0.1, 100);
camera.position.set(0, 0, 5);
camera.lookAt(0, 0, 0);

// renderer
const renderer = new THREE.WebGLRenderer();
renderer.setSize(w, h);
document.body.append(renderer.domElement);
document.body.append(stat.dom);

const orbitControls = new OrbitControls(camera, renderer.domElement);
const clock = new THREE.Clock();
tick();
function tick (){
    const time = clock.getElapsedTime();
    // console.log('time: ', time);
   frontWheels.rotation.x = time;
   backWheels.rotation.x = time;

    requestAnimationFrame(tick);
    renderer.render(scene, camera);
    stat.update();
    orbitControls.update();
}

</script>