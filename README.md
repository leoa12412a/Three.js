# Three.js 教學

在開始學習之前需做兩件事情
1. 請至<a target="_blank" href="https://threejs.org/">官方網站</a>下載範例包，其中包含許多模組與範例。
2. 請先了解一些基本元素的概念:Scene、Camera、Object、Geometry、Material、Light、Renderer。
	
## 基本元素概念

- 場景(Scene): 提供元素放置的空間。
- 相機(Camera): 在場景中使用者的視角畫面，並可以設定角度、方向等等。
- 物體(Objects): 添加在場景內的實體。
- 光源(Light): 場景內可以設定光的來源，實體有相關設定的話，three.js會自動處理光影部分。
- 渲染器(Renderer): 將設計好的場景選染到畫面上，有點像一個最底層背景的概念

更詳細的解說請參考這裡<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10199699">用 Three.js 來當個創世神 (02)：Three.js 基本元素介紹</a>

## Step1 快速開始

在開始 coding 前還有一個關於坐標的基本觀念，少了這個的話在三維場景中將很難理解各項元素的位置。
Three.js這裡是使用右手座標定位，可以使用<a target="_blank" href="https://threejs.org/docs/#api/en/helpers/AxesHelper"></a>來使用
![image](img/handed_coordinates.jpg)

首先先把剛剛下載的範例包裡的three.min.js抓出來且引入，three.js-master/build/three.min.js
```
<script src="js/main/three.min.js"></script>
```

### 1.建立場景
```
var scene = new THREE.Scene();
```

### 2.建立渲染器
```
var renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight); // 場景大小
renderer.setClearColor(0xeeeeee, 1.0); // 預設背景顏色	不設定預設就是黑色
renderer.shadowMap.enable = true; // 陰影效果

// 將渲染器的 DOM 綁到網頁上
document.body.appendChild(renderer.domElement)
```

### 3.設定相機
```
var camera = new THREE.PerspectiveCamera(45,window.innerWidth / window.innerHeight,0.1,100);
camera.position.set(10, 10, 10); // 相機位置，預設0,0,0
camera.lookAt(scene.position); // 相機焦點
```
- 視角（fov, field of view）：又稱為視野、視場，指的是我們能從畫面上看到的視野範圍，一般在遊戲中會設定在 60 ~ 90 度。
- 畫面寬高比（aspect）：渲染結果的畫面比例，一般都是使用 window.innerWidth / window.innerHeight 。
- 近面距離（near）：從距離相機多近的地方開始渲染，一般推薦使用 0.1。
- 遠面距離（far）：相機能看得多遠，一般推薦使用 1000，可視需求調整，設置過大會影響效能。

camera.lookAt:這個屬性是指相機會盯著何處，一般畫面沒也特別移動都是camera.lookAt(scene.position)，也可以追蹤某個物件移動如:<a target="_blank" href="https://threejs.org/examples/?q=lookat#misc_lookat">官方範例</a>

### 4.建立物體
```
const geometry = new THREE.BoxGeometry(1, 1, 1); // 幾何體
const material = new THREE.MeshPhongMaterial({ color: 0x0000ff }) // 材質
cube = new THREE.Mesh(geometry, material); // 建立網格物件
cube.position.set(0, 0, 0);	//設定初始位置
scene.add(cube);	//加入場景
```
幾何體建立的<a target="_blank" href="https://threejs.org/docs/index.html#api/en/geometries/BoxGeometry">官方文件</a>
各種材質(Material)的<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10192953">介紹(DAY19.Three.js 材質 Material)</a>以及
<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10204201">用 Three.js 來當個創世神 (07)：材質（Material）</a>


### 5.建立光源
物件如果使用否些材質無法顯示光影，如THREE.MeshBasicMaterial
```
var pointLight = new THREE.PointLight(0xffffff);
pointLight.position.set(10, 10, -10);
scene.add(pointLight);
```

### 6.建立動畫
```
cube.rotation.x += 0.01;	//已x軸為軸心旋轉
cube.rotation.y += 0.01;	//已y軸為軸心旋轉
```

### 7.宣染場景
```
function render()
{
  requestAnimationFrame(render);
  renderer.render(scene, camera);
}

render();
```
最後就是呼叫渲染器將剛才場景中的設定都渲染到畫面上。

而若是要讓場景中的物體動起來，就需要處理「每隔一段時間重新渲染場景」的工作，而這就是requestAnimationFrame 所負責的部分。

requestAnimationFrame 是 HTML5 中瀏覽器提供的一個為動畫而生的接口，它能讓畫面盡可能平滑、高效地進行重新渲染，還有效節省 CPU、GPU 資源，所以一般在 Three.js 會透過它來幫忙重新渲染場景。

用法可以從以上程式碼了解，在 requestAnimationFrame 中，我們放入了 render 當作它的 callback function，讓場景能不斷的被重新渲染，使得動畫能持續地進行下去。

### 8.RWD設定
```
window.addEventListener('resize', function() {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
});
```

網站參考:<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10199702">用 Three.js 來當個創世神 (03)：Hello Three.js!</a>
程式碼:<a target="_blank" href="https://github.com/leoa12412a/Three.js/blob/master/quick_start.html">連結</a>

## Step2 設計目標以及製作基本場景模型

這裡我參考<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10199744">用 Three.js 來當個創世神 (04)：專案實作</a>的設計目標，設計一個苦力怕射擊遊戲，來練習Three.js，細節部分可以參考該網站。

了結設計方向以及邏輯後，就可以開始設計我們的場景和苦力怕了。

### 1.首先跟前面一樣先建立一個基本的架構
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>My first three.js app</title>
		<style>
			body { margin: 0; }
			canvas { width: 100%; height: 100% }
		</style>
	</head>
	<body>
		<script src="js/main/three.js"></script>
		<script>
			let renderer, scene, camera;
			
			function init()
			{
				scene = new THREE.Scene();	//建立場景
				
				camera = new THREE.PerspectiveCamera(50,window.innerWidth / window.innerHeight,0.1,1000);	//畫面
				camera.position.set(60, 60, 60);
				camera.lookAt(scene.position);

				renderer = new THREE.WebGLRenderer();	//喧染器
				renderer.setSize(window.innerWidth, window.innerHeight);
				renderer.setClearColor(0xeeeeee, 1.0);	

			    let spotLight = new THREE.SpotLight(0x00a0e9);		//光源
			    spotLight.position.set(-10, 40, 30);
			    scene.add(spotLight);
								
				let axes = new THREE.AxesHelper(20);	// 三軸座標輔助
				scene.add(axes);
				
				document.body.appendChild(renderer.domElement);
			}
			
			function render() {
			  requestAnimationFrame(render);
			  renderer.render(scene, camera);
			}
			
			window.addEventListener('resize', function() {
				camera.aspect = window.innerWidth / window.innerHeight
				camera.updateProjectionMatrix()
				renderer.setSize(window.innerWidth, window.innerHeight)
			})
			
			init();
			render();
		</script>
	</body>
</html>
```

### 2.建立一個平面的地板
這裡使用一個平面幾何的方法<a target="_blank" href="https://threejs.org/docs/index.html#api/en/geometries/PlaneGeometry">官方說明</a>
```
const planeGeometry = new THREE.PlaneGeometry(60, 60);		//地板
const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xffffff });
let plane = new THREE.Mesh(planeGeometry, planeMaterial);
plane.rotation.x = -0.5 * Math.PI;	// 使平面與 y 軸垂直，並讓正面朝上
plane.position.set(0, -7, 0);
scene.add(plane);
```
這邊其中有一行 plane.rotation.x = -0.5 乘 Math.PI，是指將平面「沿著 x 軸正方向逆時針轉 90 度」。

###3 建立一個苦力怕的class

```
class Creeper
{
	constructor() 
	{
		const headGeo = new THREE.BoxGeometry(4, 4, 4);
		const bodyGeo = new THREE.BoxGeometry(4, 8, 2);
		const footGeo = new THREE.BoxGeometry(2, 3, 2);
		
		const creeperMat = new THREE.MeshPhongMaterial({ color: 0x00ff00 });
		
		// 頭
		const head = new THREE.Mesh(headGeo, creeperMat);
		head.position.set(0, 6, 0);

		// 身體
		var body = new THREE.Mesh(bodyGeo, creeperMat);
		body.position.set(0, 0, 0);
		
		// 四隻腳
		var foot1 = new THREE.Mesh(footGeo, creeperMat);
			foot1.position.set(-1, -5.5, 2);
		var foot2 = foot1.clone(); // 剩下三隻腳都複製第一隻的 Mesh
			foot2.position.set(-1, -5.5, -2);
		var foot3 = foot1.clone();
			foot3.position.set(1, -5.5, 2);
		var foot4 = foot1.clone();
			foot4.position.set(1, -5.5, -2);

		// 將四隻腳組合為一個 group
		var feet = new THREE.Group();
		feet.add(foot1);
		feet.add(foot2);
		feet.add(foot3);
		feet.add(foot4);
		
		this.creeper = new THREE.Group();
		this.creeper.add(head);
		this.creeper.add(body);
		this.creeper.add(feet);

	}
}
```
一隻苦力怕包含了一個大頭、一個長身體、四隻短腳，而這邊試著將整隻苦力怕的組件寫成一個 class 物件，在其中將四隻腳與整個身體用 THREE.Group() 層層組合起來，這樣在後續針對每一個物件下的屬性做操作會更方便。

```
function create_creeper()
{
	const creeperObj = new Creeper();
	scene.add(creeperObj.creeper);
}
```
建立一個function來呼叫產生苦力怕，並加入場景內。


```
create_creeper();
```
並再init()內呼叫create_creeper這個方法。


網站參考:<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10200287">用 Three.js 來當個創世神 (03)：Hello Three.js!</a>


## Step3 設定可以移動的視角以及FPS偵測器(畫面刷新頻率)

### 1.使用OrbitControls(軌道控制器)來調整遊戲畫面
鏡頭想要移動需要載入一個額外的模組
```
<script src="js/controls/OrbitControls.js"></script>
```

並在前面一個範例的init()方法內加入以下設定
ps:需在document.body.appendChild(renderer.domElement)下方加入
cameraControl.update()是在喧染的時候用來更新的，一般加在初始化以及宣染的函數內
```
let cameraControl;


function init()
{
	...
	
	document.body.appendChild(renderer.domElement);
				
	cameraControl = new THREE.OrbitControls( camera, renderer.domElement );
	cameraControl.enableDamping = true // 啟用阻尼效果，意思是否有慣性
	cameraControl.dampingFactor = 0.25 // 阻尼系數，鼠拖拽旋轉靈敏度
	cameraControl.update();
	
	...
}
```

```
function render() 
{
  requestAnimationFrame(render);
  cameraControl.update();
  renderer.render(scene, camera);
}
```


### 2 使用FPS偵測器

一樣需要載入一個額外的模組
```
<script src="js/controls/OrbitControls.js"></script>
```

宣告一個function來開啟偵測器
```
function initStats() 
{
	const stats = new Stats()
	stats.setMode(0) // FPS mode
	document.getElementById('stats').appendChild(stats.domElement)
	return stats
}
```

在<body>下加入一個id為stats的div
```
<div id="stats"></div>
```

也可以加入css
```
#stats {
  position: absolute;
  left: 0;
  top: 0;
}
```

並在init()以及render()也加入呼叫function跟update
```
let statsUI;

function init()
{
	...
	statsUI = initStats();	//初始化FPS監控
	...
}
```

```
function render() 
{	
	requestAnimationFrame(render);  //畫面有變動重新宣染
	cameraControl.update();
	statsUI.update(); // 需設定 update 才會持續更新
	renderer.render(scene, camera);
}
```

網站參考:<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10200289">用 Three.js 來當個創世神 (06) OrbitControls、stats.js</a>
程式碼:<a target="_blank" href="https://github.com/leoa12412a/Three.js/blob/master/shut_creeper_game.html">連結</a>


## 使用模組來載入Three.js(Import via modules)

雖然使用<script src="three.js" />來引入js既簡單又快速，但如果專案的生命週期較長的話會產生一些缺點

- You have to manually fetch and include a copy of the library as part of your project's source code
- 必須手動更新library內模組的版本
- 在檢查新版本時，容易因版本差異而被許多繁重的建構文件給擾亂(many lines of the build file)


### 使用Nodejs

若使用node.js的話，安裝方面直接使用npm就可以完成了

```
npm install three
```

在引入方面則是與一般的模組一樣的方式

```
var THREE = require('three');

var scene = new THREE.Scene();
...
```

### 使用HTML可以使用ES6 import

```
import * as THREE from 'three.module.js';  // 1.* 引入全部 2.as + 選告引入文件的變數

const scene = new THREE.Scene();
...
```
若只想引入特定的部分

```
import { Scene } from 'three';

const scene = new Scene();
...
```

### 載入時可能發生的錯誤

#### 1.必須以module形式來 import 與 export

<font style="color:red">Uncaught SyntaxError: Cannot use import statement outside a module</font>

跟 Node.js 類似，若是想要使用 import 與 export，都必須以 module 的形式來執行，所以要在 script 標籤加上 type：

```
<script type="module">

import * as THREE from 'three.module.js';

<script>
```

#### 2.必須使用伺服器方式開啟

<font style="color:red">
Access to script at 'file:///Users/huli/w_test/main.js' from origin 'null'
has been blocked by CORS policy: Cross origin requests are only supported for protocol schemes:
http, data, chrome, chrome-extension, https.
</font>

```
直接點擊文件網址開頭會是 file:///。若是要使用 import 的話，必須要用伺服器的方式開啟才行。
```

各說明以及更詳細的模組化解說可以參考<a href="https://blog.techbridge.cc/2020/01/22/webpack-%E6%96%B0%E6%89%8B%E6%95%99%E5%AD%B8%E4%B9%8B%E6%B7%BA%E8%AB%87%E6%A8%A1%E7%B5%84%E5%8C%96%E8%88%87-snowpack/">這裡</a>


## 使用Three來載入3D模組

### Three所支援的格式

Three支援許多不同格式的匯入，如下:

![image](img/loaderlist.PNG)
(實際支援請以官網為準)

### 將GLTF匯入至Three中

glTF是使用JSON標準的3D場景和模型的文件格式，也是常見的3D素材的格式之一。並是開放文檔格式(open format specification)，常用於高效能的傳輸、加載3D內容。此文件使用是以JSON（.glft）格式或二進制（.glb）格式提供，外部則是以（.jpg、.png）和額外的數據（.bin）輔助。


而要將glTF引入首先我們必須引入glTF的loader，並用new的方式宣告一個GLTFLoader

```
import { GLTFLoader } from '/jsm/loaders/GLTFLoader.js';

var loader = new GLTFLoader();
```

然後只用loader來載入檔案

```
loader.load('/model/base/scene.gltf',function (obj) {
	console.log(obj);
	scene.add(obj.scene);
}
,function ( xhr ) {
	console.log( ( xhr.loaded / xhr.total * 100 ) + '% loaded' );
},
function ( error ) {
	build_Wolf();
});
```
上面程式碼中

- 第一個fnc是載入完成
- 第二個fnc是載入中
- 第三個fnc是載入失敗

在加載後console我們載入的obj在F12裡可以看到其完整的資訊。


若要調整載入素材
- 大小可以修改物件children下的scale，預設x,y,z都是1，調整其數值可以使物件縮放
- 位置可以修改scene.position
- scene.name可以賦予名字方便選取

```
loader.load('/model/base/scene.gltf',function (obj) {
	let gltf = obj.scene.children[0];
	gltf.scale.set(20,20,20);
	obj.scene.name = 'base';
	obj.scene.position.set(0, 58, 0); 
	scene.add(obj.scene);
}
```


## 	點光源(PointLight)

點光源:從一個點向個方向發射光源，一個比較常見的例子是燈泡發出燈光，這個光可以投影出陰影

程式碼如下:
```
var light = new THREE.PointLight( 0xff0000, 1, 100 );
light.position.set( 50, 50, 50 );
scene.add( light );
```

PointLight( color : Integer, intensity : Float, distance : Number, decay : Float )

- color:光照顏色，預設為0xffffff
- intensity:光照強度
- distance:光源遞減到0的距離，數值越大，光照範圍越遠。設為0時，光照距離無限大
- decay:根據光照距離的衰減量，預設值為1


## 使用Tween.js來製作補間動畫

<a href="https://createjs.com/tweenjs">Tween.js</a>是在Create.js下的功能之一，是一個輕量的 Javascript 函式庫，主要是產生及計算位置到位置之間如何進行的動畫，只要給它起始值、結束值、過程需花費多少時間，其他的中間值會自動被計算出來。
另外<a href=""></a>Create.js下還有<a href="https://createjs.com/easeljs">Easel.js</a>、<a href="https://createjs.com/soundjs">Sound.js</a>、<a href="https://createjs.com/preloadjs">Preload.js</a>，分別的功能為輕鬆使用Canvas元素、有效的處理音效以及管理、協調數據和資源的加載


### 安裝

使用JS加載
```
<script src="https://code.createjs.com/1.0.0/tweenjs.min.js"></script>
```

使用module引入(<a href="js/main/tween.module.min.js">下載</a>)
```
import { TWEEN } from './js/main/tween.module.min.js';
```

### 開始補間

以下簡單的介紹Tween的使用方式:

```
var position = obj.position.set(0,0);
var target = { x: 300, y: 200 };
var time = 1000;

var tween = new TWEEN.Tween(position) 
	.to(target, time) 
	.easing(TWEEN.Easing.Quadratic.Out)
	.onUpdate(function() {
		console.log(position);
		console.log('running');
	})
	.onComplete(function() {
		console.log('finish');
	})
	.start();
```

- position : 初始位置
- target : 為目標位置
- time : 動畫時間(ms)
- .easing : 補間動畫效果
- .onUpdate : 在動畫期間觸發事件，在這段時間position會逐漸變成target
- .onComplete : 動畫後要觸發事件
- .start : 開始補間

<a href="https://codepen.io/mikebolt/pen/zzzvZg?editors=0010">官方範例</a>

## 粒子系統介紹

在 3D 場景中，當我們需要建立很多細小的物體時，如果一個一個創建，就需要分別管理每一顆粒子，勢必會遇到性能問題。因此在 Three.js 中，提供了 THREE.Points 這樣的粒子系統來統一管理大量的粒子。
<a href="https://stemkoski.github.io/Three.js/Particle-Engine.html">範例</a>

而在Three.js中Point的使用方式一種是以物件方式呈現，另一種則把他以材質的方式加入到期他的物件內

- Points(Objects)

```
Points( geometry : Geometry, material : Material )
```

這種粒子物件通常使用在需要比較多點建構出來的物體，並且以構造出來的點做成以點構成的幾何體，如球形物體就適合如此之運用

以球形為基礎產生點物件
```
function createSpherePoints() {
	const geometry = new THREE.SphereGeometry(20, 15, 15) // 使用球體(半徑，縱方向數量，橫方向數量)
	
	const material = new THREE.PointsMaterial({		//產生點材質
		size: 2,	//點大小		
		color: 0x00ff00 // 綠色
	})

	// 用球體與材質建立一個粒子系統
	let spherePoints = new THREE.Points(geometry, material) 
	spherePoints.position.set(40, 30, 0)
	scene.add(spherePoints)
}
```

- PointsMaterial(Materials)

材質方面就如同其他材質方式配合物件使用

```

function createVerticesPoints() {
	const geometry = new THREE.Geometry() // 先宣告一個空的幾何體
	const material = new THREE.PointsMaterial({
		size: 4,
		color: 0xff00ff // 粉紅色
	})
	
	for (let x = -5; x < 5; x++) {
		for (let y = -5; y < 5; y++) {
			
			const point = new THREE.Vector3(x * 10, y * 10, 0)  // 設定點要產生的座標位置
			
			geometry.vertices.push(point)	//將位置加入至幾何體中
		}
	}
	
	// 用前面的幾何體與材質建立一個粒子系統
	let points = new THREE.Points(geometry, material) 
	points.position.set(-45, 0, 0)
	scene.add(points)
}
```
# 在3D模組上備註
要如何達到像<a href="https://sketchfab.com/3d-models/dodo-model-ad10226b4f7a451ea23920a556c72a90">Sketchfab</a>上能在模型或是3D場景上產生類似註解的方框，並根據畫面的轉動一樣備註在其位置上?<br />
可以參考<a href="https://manu.ninja/webgl-three-js-annotations/#3">教學網站</a><br />

## 在2D canvas上畫出標記的符號
首先我們在HTML上產生一個Canvas並使用原生的Canvas語法在上面畫出1的符號，已備來當備註使用
```
<canvas id="number" width="64" height="64"></canvas>
```
其CSS
```
canvas {
	width: 100%;
	height: 100px;
	display: block;
}

#number {
	position: absolute;
	z-index: -1;
	display: none;
}
```
當然我們也可以用語法產生一個Canvas
```
document.createElement('canvas')
```
<br />
接著我們畫出一個1的符號

```
const canvas = document.getElementById('number');
const ctx = canvas.getContext('2d');
const x = 32;
const y = 32;
const radius = 30;
const startAngle = 0;
const endAngle = Math.PI * 2;

ctx.fillStyle = 'rgb(0, 0, 0)';
ctx.beginPath();
ctx.arc(x, y, radius, startAngle, endAngle);
ctx.fill();

ctx.strokeStyle = 'rgb(255, 255, 255)';
ctx.lineWidth = 3;
ctx.beginPath();
ctx.arc(x, y, radius, startAngle, endAngle);
ctx.stroke();

ctx.fillStyle = 'rgb(255, 255, 255)';
ctx.font = '32px sans-serif';
ctx.textAlign = 'center';
ctx.textBaseline = 'middle';
ctx.fillText('1', x, y);
```
如下圖就是我們用canvas匯出的標註符號
<br />
![image](img/1%E5%82%99%E8%A8%BB.PNG)

## 使用Three的sprite方法將標註加載在場景中

我們將使用Three的<a href="https://threejs.org/docs/index.html#api/en/objects/Sprite">sprite</a>方法把剛剛產生出的canvas載入我們的3D場景中<br />
sprite這個方法會讓正面永遠面向相機，如<a href="https://threejs.org/examples/?q=sprite#webgl_sprites_nodes">範例</a>所演示的樣子。<br />
先使用CanvasTexture的方法把剛剛我們畫好的Canvas標註轉換成類似皮膚(skin)之類的物件，並產生sprite專用的材質SpriteMaterial且將剛剛的皮膚加入到其中，
那我們再利用剛剛已經製作好的材質來產生sprite加入scene就可以在場景中(scene)出現了
```
const numberTexture = new THREE.CanvasTexture(
    document.querySelector('#number')
);

const spriteMaterial = new THREE.SpriteMaterial({
    map: numberTexture,
    alphaTest: 0.5,
    transparent: true,
    depthTest: false,
    depthWrite: false
});

sprite = new THREE.Sprite(spriteMaterial);
sprite.position.set(250, 250, 250);
sprite.scale.set(35, 35, 1);

scene.add(sprite);
```
如下圖就是將sprite加入場景中
![image](img/Sprite.png)

## 將HTML備註的文字永遠保持在備註的旁邊
先產生html的文字
```
<div class="annotation">
	<p><strong>Cube</strong></p>
	<p>In geometry, a cube is a three-dimensional solid object bounded by six square faces, facets or sides, with three meeting at each vertex.</p>
</div>
```
其CSS
```
.annotation {
	position: absolute;
	top: 0;
	left: 0;
	z-index: 1;
	margin-left: 15px;
	margin-top: 15px;
	padding: 1em;
	width: 200px;
	color: #fff;
	background: rgba(0, 0, 0, 0.8);
	border-radius: .5em;
	font-size: 12px;
	line-height: 1.2;
	transition: opacity .5s;
	&::before {
		content: '1';
		position: absolute;
		top: -30px;
		left: -30px;
		width: 30px;
		height: 30px;
		border: 2px solid #fff;
		border-radius: 50%;
		font-size: 16px;
		line-height: 30px;
		text-align: center;
		background: rgba(0, 0, 0, 0.8);
	}
}
```
那接下來我們只需寫一個function運用數學式的計算將備註文字保持在我們的備註旁邊，Vector3這個方法是幫我們產生一個3D的座標並將整個場景(renderer.domElement)宣告告成canvas，
並利用整個場景的大小及標註的位置來計算出HTML文字需要存在的位置
```
function update_text()
{
	const vector = new THREE.Vector3(3, 20.5, 1);
	const canvas = renderer.domElement;
	
	vector.project(camera);
	
	vector.x = Math.round((0.5 + vector.x / 2) * (canvas.width / window.devicePixelRatio));
	vector.y = Math.round((0.5 - vector.y / 2) * (canvas.height / window.devicePixelRatio));
	
	annotation.style.top = `${vector.y}px`;
	annotation.style.left = `${vector.x}px`;
}
```
且我們將這個function放入render()內，這樣每次的宣染都會重新再將正確的位置告訴HTML
```
function render()
{
	requestAnimationFrame(render);
	controls.update();
	renderer.render(scene, camera);
	update_text();
}
```

