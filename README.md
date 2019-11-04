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
![img]('img/handed_coordinates.jpg')

首先先把剛剛下載的範例包裡的three.min.js抓出來且引入，three.js-master/build/three.min.js
```
<script src="js/main/three.js"></script>
```

1. 建立場景
```
var scene = new THREE.Scene();
```

2. 建立渲染器
```
var renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight); // 場景大小
renderer.setClearColor(0xeeeeee, 1.0); // 預設背景顏色	不設定預設就是黑色
renderer.shadowMap.enable = true; // 陰影效果

// 將渲染器的 DOM 綁到網頁上
document.body.appendChild(renderer.domElement)
```

3. 設定相機
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

4. 建立物體
```
const geometry = new THREE.BoxGeometry(1, 1, 1); // 幾何體
const material = new THREE.MeshNormalMaterial({ color: 0x0000ff }); // 材質
cube = new THREE.Mesh(geometry, material); // 建立網格物件
cube.position.set(0, 0, 0);	//設定初始位置
scene.add(cube);	//加入場景
```
幾何體建立的<a target="_blank" href="https://threejs.org/docs/index.html#api/en/geometries/BoxGeometry">官方文件</a>
各種材質(Material)的<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10192953">介紹(DAY19.Three.js 材質 Material)</a>


5. 建立光源
物件如果使用否些材質無法顯示光影，如THREE.MeshBasicMaterial
```
var pointLight = new THREE.PointLight(0xffffff);
pointLight.position.set(10, 10, -10);
scene.add(pointLight);
```

6. 建立動畫
```
cube.rotation.x += 0.01;	//已x軸為軸心旋轉
cube.rotation.y += 0.01;	//已y軸為軸心旋轉
```

7. 宣染場景
```
requestAnimationFrame(render);
renderer.render(scene, camera);
```
最後就是呼叫渲染器將剛才場景中的設定都渲染到畫面上。

而若是要讓場景中的物體動起來，就需要處理「每隔一段時間重新渲染場景」的工作，而這就是requestAnimationFrame 所負責的部分。

requestAnimationFrame 是 HTML5 中瀏覽器提供的一個為動畫而生的接口，它能讓畫面盡可能平滑、高效地進行重新渲染，還有效節省 CPU、GPU 資源，所以一般在 Three.js 會透過它來幫忙重新渲染場景。

用法可以從以上程式碼了解，在 requestAnimationFrame 中，我們放入了 render 當作它的 callback function，讓場景能不斷的被重新渲染，使得動畫能持續地進行下去。

8. RWD設定
```
window.addEventListener('resize', function() {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
});
```

網站參考:<a target="_blank" href="https://ithelp.ithome.com.tw/articles/10199702">用 Three.js 來當個創世神 (03)：Hello Three.js!</a>