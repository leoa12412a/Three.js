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
const material = new THREE.MeshNormalMaterial({ color: 0x0000ff }); // 材質
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

###2.建立一個平面的地板
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
