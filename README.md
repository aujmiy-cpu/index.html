index.html<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>3D Shooter ULTRA</title>

<style>
body { margin:0; overflow:hidden; background:black; }
#shoot { position:absolute; bottom:30px; right:30px; padding:20px; background:red; color:white; }
</style>

<script type="importmap">
{
 "imports": {
  "three": "https://unpkg.com/three@0.160.0/build/three.module.js"
 }
}
</script>
</head>

<body>

<button id="shoot">إطلاق</button>

<script type="module">
import * as THREE from 'three';

let scene = new THREE.Scene();
scene.fog = new THREE.Fog(0x000000, 10, 50);

let camera = new THREE.PerspectiveCamera(75, innerWidth/innerHeight, 0.1, 1000);
let renderer = new THREE.WebGLRenderer({antialias:true});

renderer.setSize(innerWidth, innerHeight);
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.setPixelRatio(window.devicePixelRatio);

document.body.appendChild(renderer.domElement);

camera.position.set(0,2,5);

// أرضية
let floor = new THREE.Mesh(
 new THREE.PlaneGeometry(100,100),
 new THREE.MeshStandardMaterial({color:0x222222})
);
floor.rotation.x = -Math.PI/2;
floor.receiveShadow = true;
scene.add(floor);

// إضاءة قوية
let light = new THREE.DirectionalLight(0xffffff, 2);
light.position.set(10,20,10);
light.castShadow = true;
light.shadow.mapSize.width = 2048;
light.shadow.mapSize.height = 2048;
scene.add(light);

// إضاءة محيطية
scene.add(new THREE.AmbientLight(0x404040));

// لاعب
let player = new THREE.Mesh(
 new THREE.BoxGeometry(),
 new THREE.MeshStandardMaterial({color:0x00ff88, metalness:0.5, roughness:0.2})
);
player.castShadow = true;
scene.add(player);

let bullets = [];
let enemies = [];

// إطلاق
document.getElementById("shoot").onclick = () => {
 let bullet = new THREE.Mesh(
  new THREE.SphereGeometry(0.1,16,16),
  new THREE.MeshStandardMaterial({color:0xffff00, emissive:0xffff00})
 );
 bullet.position.copy(player.position);
 bullet.castShadow = true;
 scene.add(bullet);
 bullets.push(bullet);
};

// أعداء
function spawnEnemy(){
 let enemy = new THREE.Mesh(
  new THREE.BoxGeometry(),
  new THREE.MeshStandardMaterial({color:0xff0000})
 );
 enemy.position.set((Math.random()-0.5)*20, 0.5, -20);
 enemy.castShadow = true;
 scene.add(enemy);
 enemies.push(enemy);
}
setInterval(spawnEnemy, 1000);

// تحديث
function update(){

 bullets.forEach((b,i)=>{
  b.position.z -= 1;

  enemies.forEach((e,j)=>{
   if(b.position.distanceTo(e.position) < 1){
    scene.remove(b);
    scene.remove(e);
    bullets.splice(i,1);
    enemies.splice(j,1);
   }
  });
 });

 enemies.forEach(e=>{
  e.position.z += 0.1;
 });
}

// تشغيل
function animate(){
 requestAnimationFrame(animate);
 update();
 renderer.render(scene,camera);
}
animate();

</script>
</body>
</html>
