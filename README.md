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
    let ani = [];
    for (let i = 1; i <= 50; i++) {
      let temp = i;
      ani.push(`./imgs/ani/${700+temp}.png`);
    }

    // 女孩
    let girl = [];
    for (let i = 0; i <= 62; i++) {
      let temp = i;
      girl.push(`./imgs/girl/${160+temp}.png`);
    }

    // 飞机
    let planeArr = [];
    for (let i = 8; i <= 33; i++) {
      let temp = i;
      planeArr.push(`./imgs/plane/${400+temp}.png`);
    }
    // 物件
    let itemsArr = [];
    for (let i = 1; i <= 7; i++) {
      itemsArr.push(`./imgs/items/${i}.png`)
    }
    // loader素材
    PIXI.loader.add([ani,girl]).on("progress",(loader,resources)=>{

      // 这里是样式，在过程中可以做一个百分比进度条加载
      body.style.height = window.screen.height;
      body.style.width = window.screen.width;
      loading.style.position="absolute";
      speed_container.style.position="absolute";
      loading.style.left=`${window.screen.width / 2}px`
      loading.style.top=`${window.screen.height / 2}px`
      speed_container.style.left=`${(window.screen.width-300)/2}px`
      speed_container.style.top=`${window.screen.height / 2+50}px`
      loading.innerText =parseInt(loader.progress)+'%';
      speed.style.width = `${parseInt(loader.progress)}%`;
      
      // 如果加载完成则隐藏进度条
      if (parseInt(loader.progress) == 100) {
        loading.innerText = ""
        speed_container.style.display="none";
      } // 加载完成执行函数setup
    }).load(setup);
```
3、创建需要的精灵、根据精灵需要出现的位置设定精灵位置与大小、将精灵添加到舞台。渲染到页面。<br/>
```javascript
    // 加载完成执行函数
    function setup(){
      // 创建精灵
      // 漩涡精灵
      let aniSpr = new PIXI.Sprite.fromImage(ani[0]);
      // 女孩精灵
      let girlSpr = new PIXI.Sprite.fromImage(girl[0]);
      // 飞机精灵
      let textureArr = [];
      for (let i = 0; i < planeArr.length; i++) {
        let texture = PIXI.Texture.from(planeArr[i]);
        textureArr.push(texture);
      }
      let planeSpr = new PIXI.extras.AnimatedSprite(textureArr);
      // 物件精灵
      let itemsSpr1 = new PIXI.Sprite.fromImage(itemsArr[0]);
      let itemsSpr2 = new PIXI.Sprite.fromImage(itemsArr[1]);
      let itemsSpr3 = new PIXI.Sprite.fromImage(itemsArr[2]);
      let itemsSpr4 = new PIXI.Sprite.fromImage(itemsArr[3]);
      let itemsSpr5 = new PIXI.Sprite.fromImage(itemsArr[4]);
      let itemsSpr6 = new PIXI.Sprite.fromImage(itemsArr[5]);
      let itemsSpr7 = new PIXI.Sprite.fromImage(itemsArr[6]);
      // 文字精灵
      let text = new PIXI.Text("向上滑动，找回童年",style);
      // 设置中心轴
      aniSpr.anchor.set(0.5);
      girlSpr.anchor.set(0.5);
      planeSpr.anchor.set(0.5);
      text.anchor.set(0.5);
      itemsSpr1.anchor.set(0.5);
      itemsSpr2.anchor.set(0.5);
      itemsSpr3.anchor.set(0.5);
      itemsSpr4.anchor.set(0.5);
      itemsSpr5.anchor.set(0.5);
      itemsSpr6.anchor.set(0.5);
      itemsSpr7.anchor.set(0.5);

      // 精灵位置
      // 漩涡
      aniSpr.position.set(width / 2, height / 2);
      // 女孩
      girlSpr.position.set(width / 2, -girlSpr.height / 2);
      // 物件
      // 左边
      itemsSpr1.position.set(0,height+150); // 距离可以根据自己需要来定
      itemsSpr3.position.set(-200,height+2550);
      itemsSpr5.position.set(-500,height+4950);
      itemsSpr7.position.set(-800,height+7500);
      // 右边
      itemsSpr2.position.set(width,height+900);
      itemsSpr4.position.set(width,height+3300);
      itemsSpr6.position.set(width,height+5700);
      // 飞机
      planeSpr.position.set(width / 2, height / 2 - 50);
      // 文字
      text.position.set(width / 2, height / 2 - 100);

      // 精灵缩放
      aniSpr.scale.set(0.55);
      girlSpr.scale.set(0.6);
      planeSpr.scale.set(0);
      itemsSpr1.scale.set(2);
      itemsSpr2.scale.set(2);
      itemsSpr3.scale.set(6);
      itemsSpr4.scale.set(6);
      itemsSpr5.scale.set(6);
      itemsSpr6.scale.set(6);
      itemsSpr7.scale.set(14);

      // 透明
      aniSpr.alpha = 0;
      girlSpr.alpha = 1;
      planeSpr.alpha = 0;

      // 添加到舞台
      app.stage.addChild(aniSpr,girlSpr,planeSpr,text,itemsSpr1,itemsSpr2,itemsSpr3,itemsSpr4,itemsSpr5,itemsSpr6,itemsSpr7);
```
4、创建一个总时间轴。<br/>
```javascript
    // 创建一个总时间轴
      let timeList = new gsap.timeline({paused: true});
      let moveMin = -2000;
```
# 二、AlloyTouch
实例一个AlloyTouch对象：
```javascript
    let toch = new AlloyTouch({
        touch: document.getElementById("stage"), // 需要监听滑动的dom元素
        maxSpeed: 0.4, // 最大滑动速度
        target:{y:0}, // 滑动对象
        property: 'y', // 被滑动属性
        min: -2000, // 滑动最小值
        max: 0, // 滑动最大值
        value: 0, // 初始值
        change:function(value){ // 监听滑动
          let myProgress = value / moveMin; // 初始值/滑动最小值，最小值自己定 = 滑动距离
          timeList.seek(myProgress); // 从总时间轴中定位到myProgress时间点
          animatePlay(myProgress); // 序列帧动画
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
      let textTwenn = gsap.to(text,0.05,{alpha:0});
      let textTwenn2 = gsap.from(text,1,{y:height / 2 -70,onComplete:()=>{
        textTwenn2.reverse(0);
      },onReverseComplete:()=>{
        textTwenn2.play();
      }})

      // 背景1动画，在整体滚动的0.1完成执行透明到显示
      let twenn1 = gsap.to(aniSpr,0.4,{alpha: 1});
      let twenn2 = gsap.to(aniSpr,1,{alpha: 0});

      // 女孩动画
      let girlTwenn1 = gsap.to(girlSpr,0.1,{y:height/2});
      let girlTwenn2 = gsap.to(girlSpr,0.7,{y:height+girlSpr.height/2});

      // 物件动画
      let itemsTwenn1 = gsap.to(itemsSpr1.position,0.2,{x:100,y:-150});
      let itemsTwenn2 = gsap.to(itemsSpr1.scale,0.2,{x:0.1,y:0.1});
      let items2Twenn1 = gsap.to(itemsSpr2.position,0.2,{x:width-100,y:-150});
      let items2Twenn2 = gsap.to(itemsSpr2.scale,0.2,{x:0.1,y:0.1});
      let items3Twenn1 = gsap.to(itemsSpr3.position,0.2,{x:100,y:-150});
      let items3Twenn2 = gsap.to(itemsSpr3.scale,0.2,{x:0.1,y:0.1});
      let items4Twenn1 = gsap.to(itemsSpr4.position,0.2,{x:width-100,y:-150});
      let items4Twenn2 = gsap.to(itemsSpr4.scale,0.2,{x:0.1,y:0.1});
      let items5Twenn1 = gsap.to(itemsSpr5.position,0.2,{x:100,y:-150});
      let items5Twenn2 = gsap.to(itemsSpr5.scale,0.2,{x:0.1,y:0.1});
      let items6Twenn1 = gsap.to(itemsSpr6.position,0.2,{x:width-100,y:-150});
      let items6Twenn2 = gsap.to(itemsSpr6.scale,0.2,{x:0.1,y:0.1});
      let items7Twenn1 = gsap.to(itemsSpr7.position,0.2,{x:100,y:-150});
      let items7Twenn2 = gsap.to(itemsSpr7.scale,0.2,{x:0.1,y:0.1});

      //飞机动画
      let planeTwenn1 = gsap.to(planeSpr.scale,0.3,{x:0.4,y:0.4})
      let planeTwenn2 = gsap.to(planeSpr,0.3,{alpha:1})

      
```
2、把动画添加到时间轴。<br/>
```javascript
    // 把动画添加到子时间轴
      t1.add(textTwenn,0).add(twenn1,0)
      .add(girlTwenn1,0).add(girlTwenn2,0.5)
      .add(itemsTwenn1,0.21).add(itemsTwenn2,0.21)
      .add(items2Twenn1,0.22).add(items2Twenn2,0.22)
      .add(items3Twenn1,0.23).add(items3Twenn2,0.23)
      .add(items4Twenn1,0.24).add(items4Twenn2,0.24)
      .add(items5Twenn1,0.25).add(items5Twenn2,0.25)
      .add(items6Twenn1,0.26).add(items6Twenn2,0.26)
      .add(items7Twenn1,0.27).add(items7Twenn2,0.27)
      .add(planeTwenn1,0.6).add(planeTwenn2,0.6);
      t1.add(twenn2,0.4);

      // 把子时间轴添加到总时间轴
      timeList.add(t1,0);
```
3、序列帧动画。根据AlloyTouch监听用户滑动距离来实现序列帧动画图片切换。<br/>
```javascript
    function animatePlay(progress){
        // 从总时间轴0.01开始
        if (progress>=0.01) {
          let imgArrNum = ani.length;
          // 根据滑动的数值获取要切换图片的下标
          let index =Math.floor((progress - 0.01) / 0.4 * imgArrNum);
          let temp2 = index;
          if (temp2<imgArrNum) {
            // 改变精灵
            aniSpr.texture = new PIXI.Texture.fromImage(`./imgs/ani/${700+temp2}.png`);
          }

          // 女孩动画
          let imgArrNum2 = girl.length;
          let index2 =Math.floor((progress - 0.01) / 0.7 * imgArrNum2);
          let temp3 = index2;
          if (progress>=0.05) {
            if (temp3< imgArrNum2) {
              girlSpr.texture = new PIXI.Texture.fromImage(`./imgs/girl/${160+temp3}.png`)
            }
          }
        }
        if (progress>=0.4) {
          let imgArrNum = ani.length;
          let index =Math.floor((progress - 0.01) / 0.4 * imgArrNum);
          let temp2 = index;
          if (temp2>imgArrNum) {
            if (800-temp2>700) {
              // 改变精灵
              aniSpr.texture = new PIXI.Texture.fromImage(`./imgs/ani/${800-temp2}.png`);
            }
          }
        }
      }

      // 飞机逐帧动画
      planeSpr.animationSpeed = 0.2;
      planeSpr.play();
```
# 四、结语
`博主声明（项目中素材来源于网络，仅供交流学习使用，切勿商用！）;`
<br/>
<br/>
本人是一个刚入职的前端，因为最近会又一个项目是基于PIXI.JS+GSAP开发的h5，pixi.js文档与demo比较少，只能看官方文档和API、刚开始完全不知道他们两个怎么结合使用，通过这个案例之后就明朗了，据我菜鸟的经验直白讲就是pixi.js只负责渲染，GSAP负责动画，可能是之前想的太复杂了，哈哈。这个案例虽然比较简单，但对初学者来说感觉挺不错的，希望可以帮助到一些跟我一样正在学pixi+gsap的一些兄台。最后代码可能有点冗余和简陋，见谅...
