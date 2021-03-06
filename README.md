# 前提声明
这个案例是借鉴一位博主的demo实现的，原地址：<https://momentstudio.coding.net/public/long-take-demo/long-take-demo/git>
<br/>
<br/>
<br/>
<br/>
# 选用技术
1、采用<a href="https://github.com/Zainking/learningPixi#text">pixi.js</a>绘制画面。<br/>
2、采用<a href="https://greensock.com/docs/v3">GSAP</a>实现动画效果。<br/>
3、采用<a href="https://github.com/AlloyTeam/PhyTouch">AlloyTouch</a>监听用户手指滑动控制动画时间轴。
<br/>
<br/>
<br/>
<br/>
# 实现思路
## HTML
```javascript
    <audio id="audio" loop="loop" preload="auto" src="./medio/bg.mp3"></audio>
  <div id="stage">
    <div id="loading"></div>
    <div id="speed_container">
      <div id="speed"></div>
    </div>
  </div>
    // 获取dom元素
    let body = document.body;
    let audio = document.getElementById("audio");
    let loading = document.getElementById("loading");
    let speed_container = document.getElementById("speed_container");
    let speed = document.getElementById("speed");
```
## CSS
```javascript
   *{
      margin: 0;
      padding: 0;
    }
    body{
      background-color: #FFE4B5;
    }
    #stage{
      display: flex;
      justify-items: center;
      align-items: center;
      color: #FF9900;
    }
    #speed_container{
      width: 300px;
      height: 1px;
      border-radius: 25px;
    }
    #speed{
      width: 0;
      height: 1px;
      background-color: #FF9900;
      border-radius: 25px;
    }
```
## 引入文件
```javascript
    <script src="./js/Pixi.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.5.0/gsap.min.js"></script>
    <script src="./js/Alloytouch.js"></script>
```
## 一、PIXI.JS

将场景所需要的图片利用pixi.js绘制出来。<br/>
1、创建PIXI应用。<br/>

```javascript
    const width = window.screen.width;
    const height = window.screen.height;
    let app = new PIXI.Application(width,height , {
      backgroundColor: 0xFFE4B5,
    });
    // 渲染到页面
    document.getElementById("stage").appendChild(app.view);
```
2、加载图片素材。<br/>
```javascript
    // 漩涡
    let aniArr = [];
    let aniArr2 = []; // 反转
    let ani = []; // 预加载
    for (let i = 1; i <= 50; i++) {
      aniArr.push(PIXI.Texture.from(`./imgs/ani/${700 + i}.png`));
      aniArr2.unshift(PIXI.Texture.from(`./imgs/ani/${700 + i}.png`));
      ani.push(`./imgs/ani/${700 + i}.png`);
    }

    // 女孩
    let girlArr = [];
    let girl = []; // 预加载
    for (let i = 0; i <= 62; i++) {
      girlArr.push(PIXI.Texture.from(`./imgs/girl/${160 + i}.png`));
      girl.push(`./imgs/girl/${160 + i}.png`);
    }

    // 飞机
    let planeArr = [];
    let plane = []; // 预加载
    for (let i = 8; i <= 33; i++) {
      planeArr.push(PIXI.Texture.from(`./imgs/plane/${400 + i}.png`));
      plane.push(`./imgs/plane/${400 + i}.png`);
    }
    // 物件
    let itemsArr = [];
    let items = []; // 预加载
    for (let i = 1; i <= 7; i++) {
      itemsArr.push(PIXI.Texture.from(`./imgs/items/${i}.png`));
      items.push(`./imgs/items/${i}.png`);
    }
    // 文字样式
    let style =new PIXI.TextStyle({
      fontFamily: 'Arial',
      fontSize: '20px',
      fill: '#FF9900',
    })
    // loader素材
    PIXI.loader.add([ani, girl, plane, items]).on("progress", (loader, resources)=>{
      // 这里是样式，做了个百分比加载动画
      body.style.height = window.screen.height;
      body.style.width = window.screen.width;
      loading.style.position = "absolute";
      speed_container.style.position = "absolute";
      loading.style.left = `${window.screen.width / 2}px`;
      loading.style.top = `${window.screen.height / 2}px`;
      speed_container.style.left = `${(window.screen.width - 300) / 2}px`;
      speed_container.style.top = `${window.screen.height / 2 + 50}px`;
      loading.innerText = parseInt(loader.progress) + '%';
      speed.style.width = `${parseInt(loader.progress)}%`;
      // 加载完成隐藏
      if (Math.ceil(loader.progress) == 100) {
        loading.innerText = "";
        speed_container.style.display= "none";
      }
    }).load(setup);
```
3、创建需要的精灵、根据精灵需要出现的位置设定精灵位置与大小、将精灵添加到舞台。渲染到页面。后面所有代码都写在setup里。<br/>
```javascript
        // 加载完成执行函数
    function setup(){
      // 创建精灵
      // 文字精灵
      let text = new PIXI.Text("向上滑动，找回童年", style);
      // 漩涡精灵
      let aniSpr = new PIXI.Sprite(aniArr[0]);
      // 女孩精灵
      let girlSpr = new PIXI.Sprite(girlArr[0]);
      // 物件精灵
      let itemsSpr1 = new PIXI.Sprite(itemsArr[0]);
      let itemsSpr2 = new PIXI.Sprite(itemsArr[1]);
      let itemsSpr3 = new PIXI.Sprite(itemsArr[2]);
      let itemsSpr4 = new PIXI.Sprite(itemsArr[3]);
      let itemsSpr5 = new PIXI.Sprite(itemsArr[4]);
      let itemsSpr6 = new PIXI.Sprite(itemsArr[5]);
      let itemsSpr7 = new PIXI.Sprite(itemsArr[6]);
      // 飞机精灵
      let planeSpr = new PIXI.extras.AnimatedSprite(planeArr);

      // 设置中心轴
      text.anchor.set(0.5);
      aniSpr.anchor.set(0.5);
      girlSpr.anchor.set(0.5);
      planeSpr.anchor.set(0.5);
      itemsSpr1.anchor.set(0.5);
      itemsSpr2.anchor.set(0.5);
      itemsSpr3.anchor.set(0.5);
      itemsSpr4.anchor.set(0.5);
      itemsSpr5.anchor.set(0.5);
      itemsSpr6.anchor.set(0.5);
      itemsSpr7.anchor.set(0.5);

      // 精灵位置
      text.position.set(width / 2, height / 2 - 100);
      aniSpr.position.set(width / 2, height / 2);
      girlSpr.position.set(width / 2, -girlSpr.height / 2);
      itemsSpr1.position.set(0, height + 300);
      itemsSpr2.position.set(width, height + 300);
      itemsSpr3.position.set(0, height + 300);
      itemsSpr4.position.set(width, height + 300);
      itemsSpr5.position.set(0, height + 300);
      itemsSpr6.position.set(width, height + 300);
      itemsSpr7.position.set(0, height + 300);
      planeSpr.position.set(width / 2, height / 2 - 50);

      // 精灵缩放
      aniSpr.scale.set(0.55);
      girlSpr.scale.set(0.6);
      itemsSpr1.scale.set(1.5);
      itemsSpr2.scale.set(1.5);
      itemsSpr3.scale.set(1.5);
      itemsSpr4.scale.set(1.5);
      itemsSpr5.scale.set(1.5);
      itemsSpr6.scale.set(1.5);
      itemsSpr7.scale.set(1.5);
      planeSpr.scale.set(0);
      // 透明
      aniSpr.alpha = 0;
      planeSpr.alpha = 0;

      // 添加到舞台
      app.stage.addChild(text, aniSpr, girlSpr, planeSpr, itemsSpr1, itemsSpr2, itemsSpr3, itemsSpr4, itemsSpr5, itemsSpr6, itemsSpr7);

```
4、创建一个总时间轴。<br/>
```javascript
    // 创建一个总时间轴
    let timeList = new gsap.timeline({paused: true});
```
# 二、AlloyTouch
实例一个AlloyTouch对象：
```javascript
    let toch = new AlloyTouch({
        touch: document.getElementById("stage"),
        maxSpeed: 0.4, // 最大滑动速度
        target: {y:0}, // 滑动对象
        property: 'y', // 被滑动属性
        min: -6000, // 滑动最小值
        max: 0, // 滑动最大值
        value: 0, // 初始值
        change: function(value){
          let move = value / -2000;
          audio.play();
          timeList.seek(move);
          animatePlay(move);
        }
      });
```
# 三、GSAP
1、创建一个时间轴。<br/>
```javascript
    // 创建一个时间轴
    let t1 = new gsap.timeline({delay: 0});
```
2、根据需求设定动画。<br/>
```javascript
    // 创建一个时间轴
    // 文字动画
      let textTwenn = gsap.to(text, 0.05, {alpha: 0});
      let textTwenn2 = gsap.from(text, 1, {y: height / 2 -70,
      onComplete: ()=>{
        textTwenn2.reverse(0);
      },onReverseComplete: ()=>{
        textTwenn2.play();
      }});

      // 漩涡动画
      let aniTwenn1 = gsap.to(aniSpr, 0, {alpha: 1});
      let aniTwenn2 = gsap.to(aniSpr, 2, {alpha: 0});

      // 女孩动画
      let girlTwenn1 = gsap.to(girlSpr, 0.5, {y: height/2});
      let girlTwenn2 = gsap.to(girlSpr, 1.5, {y: height + girlSpr.height / 2});

      // 物件动画
      let items1Tween1 = gsap.to(itemsSpr1.scale, 0.5, { x: 0.2, y: 0.2 });
      let items1Tween2 = gsap.to(itemsSpr1.position, 0.5, { x: 100, y: -100 });
      let items2Tween1 = gsap.to(itemsSpr2.scale, 0.5, { x: 0.2, y: 0.2 });
      let items2Tween2 = gsap.to(itemsSpr2.position, 0.5, { x: width - 100, y: -100 });
      let items3Tween1 = gsap.to(itemsSpr3.scale, 0.5, { x: 0.2, y: 0.2 });
      let items3Tween2 = gsap.to(itemsSpr3.position, 0.5, { x: 100, y: -100 });
      let items4Tween1 = gsap.to(itemsSpr4.scale, 0.5, { x: 0.2, y: 0.2 });
      let items4Tween2 = gsap.to(itemsSpr4.position, 0.5, { x: width - 100, y: -100 });
      let items5Tween1 = gsap.to(itemsSpr5.scale, 0.5, { x: 0.2, y: 0.2 });
      let items5Tween2 = gsap.to(itemsSpr5.position, 0.5, { x: 100, y: -100 });
      let items6Tween1 = gsap.to(itemsSpr6.scale, 0.5, { x: 0.2, y: 0.2 });
      let items6Tween2 = gsap.to(itemsSpr6.position, 0.5, { x: width - 100, y: -100 });
      let items7Tween1 = gsap.to(itemsSpr7.scale, 0.5, { x: 0.2, y: 0.2 });
      let items7Tween2 = gsap.to(itemsSpr7.position, 0.5, { x: 100, y: -100 });

      //飞机动画
      let planeTwenn1 = gsap.to(planeSpr.scale, 0.5,{x: 0.4,y: 0.4});
      let planeTwenn2 = gsap.to(planeSpr, 0.5, {alpha: 1});

      
```
2、把动画添加到时间轴。<br/>
```javascript
    // 把动画添加到子时间轴
      t1.add(textTwenn, 0).add(aniTwenn1,0.1).add(aniTwenn2, 1.5)
        .add(girlTwenn1, 0).add(girlTwenn2, 2.1)
        .add(items1Tween1, 0.8).add(items1Tween2, 0.8)
        .add(items2Tween1, 0.9).add(items2Tween2, 0.9)
        .add(items3Tween1, 1).add(items3Tween2, 1)
        .add(items4Tween1, 1.1).add(items4Tween2, 1.1)
        .add(items5Tween1, 1.2).add(items5Tween2, 1.2)
        .add(items6Tween1, 1.3).add(items6Tween2, 1.3)
        .add(items7Tween1, 1.4).add(items7Tween2, 1.4)
        .add(planeTwenn1, 2.5).add(planeTwenn2, 2.5);

      // 把子时间轴添加到总时间轴
      timeList.add(t1, 0);
```
3、序列帧动画。根据AlloyTouch监听用户滑动距离来实现序列帧动画图片切换。<br/>
```javascript
    function animatePlay (params) {
        // 漩涡
        let aniArrSum = aniArr.length;
        if (params < 1.5 && params > 0) {
            let img = aniArr[Math.floor(params / 1.5 * aniArrSum)];
            aniSpr.texture = new PIXI.Texture.from(img.textureCacheIds[0]);
        } else {
            if (params < 3 && params > 0) {
                let img = aniArr2[Math.floor((params / 1.5 * aniArrSum) - aniArrSum)];
                aniSpr.texture = new PIXI.Texture.from(img.textureCacheIds[0]);
            }
        }
        // 女孩
        let girlArrSum = girlArr.length;
        if (params < 3 && params > 0) {
            let img = girlArr[Math.floor(params / 3 * girlArrSum)];
            girlSpr.texture = new PIXI.Texture.from(img.textureCacheIds[0]);
        }
    };

    // 飞机逐帧动画
      planeSpr.animationSpeed = 0.2;
      planeSpr.play();
```
# 四、结语
`博主声明（项目中素材来源于网络，仅供交流学习使用，切勿商用！）;`
<br/>
<br/>
本人是一个刚入职的前端，因为最近会又一个项目是基于PIXI.JS+GSAP开发的h5，pixi.js文档与demo比较少，只能看官方文档和API、刚开始完全不知道他们两个怎么结合使用，通过这个案例之后就明朗了，据我菜鸟的经验直白讲就是pixi.js只负责渲染，GSAP负责动画，可能是之前想的太复杂了，哈哈。这个案例虽然比较简单，但对初学者来说感觉挺不错的，希望可以帮助到一些跟我一样正在学pixi+gsap的一些兄台。最后代码可能有点冗余和简陋，见谅...
