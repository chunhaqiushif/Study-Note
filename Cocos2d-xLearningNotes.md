### Cocos2d-x Learning Notes:
### the Defintive Guide to C++ API and Game Projects Development
#### 《Cocos2d-x 学习笔记：完全掌握C++ API与游戏项目开发》
#### 作者：赵志荣 关东升

---
### 第三章 Cocos2d-引擎
---
#### 3.4 Cocos2d-x核心概念
##### 3.4.1 导演
导演类Director（v3.0之前是CCDirector）用于保管场景对象，采用单例设计模式，在整个工程中只有一个实例对象。由于单例模式能够保存一致的配置指信息，便于管理场景对象。
获得导演类Director的示例语句如下：
```
auto director = Director::getInstance();
```
导演对象职责如下：
- 访问和改变场景；
- 访问Cocosd-x的配置信息；
- 暂停、继续和停止游戏；
- 转换坐标。

```
Director类图：
Ref ← Director ← DisplayLinkDirector
```

##### 3.4.2 场景
场景类Scene（v3.0之前是CCScene）是构成游戏的界面，类似于电影中的场景。场景大致可以分为以下几类：
- 展示场景类：播放视频或简单地在图像上输出文字，来实现游戏的开场介绍、胜利和失败提示、帮助介绍。
- 选项类场景：主菜单、设置游戏参数等。
- 游戏场景：这是游戏的主要内容。

```
Scene类图：
Ref ← Node ← Scene ← TransitionScene
```
##### 3.4.3 层
```
Layer类图：
Ref ← Node ← Layer
```
----
#### 3.5 Node与Node层级架构
##### 3.5.1 Node中的重要操作
Node作为根类，他有很多重要的函数：
- 创建节点：```Node* childNode = Node::create();```
- 增加新的子节点：```node -> addChild(childNode,0,123);```第二个参数Z轴绘制顺序，第三个参数是标签。
- 查找子节点：```Node* node = node -> getChildByTag(123);```通过标签查找子节点。
- ```node -> removeChildByTag(123, true);```通过标签删除子节点，并停止所有该节点上的一切动作。
- ```node -> removeChild(childNode, true);```删除childNode节点，并停止这些子节点上的一切动作。
- ```node -> removeAllChildrenWithCleanup(true);```删除node节点的所有子节点，并停止这些节点上的一切动作。
- ```node -> removeFromParentAndCleanup(true);```从父节点删除node节点，并停止所有该节点上的一切动作。

##### 3.5.2 Node中的重要属性
position和anchorpoint：
position(位置)属性是Node对象的实际位置。往往与anchorpoint（锚点）属性配合使用，为了将一个Node对象精准地放在屏幕某个位置上，需要设置该对象的anchorPoint，其属性相对于position的比例。
##### 3.5.3 游戏循环与调度
每一个游戏程序都有一个循环在不断运行，它由导演对象来管理和维护。如果需要场景中的精灵运动起来，我们可以在游戏循环中使用定时器（Scheduler）对精灵等对象的运行进行调度。因为Node类封装了Scheduler类，所以我们也可以直接使用Node中的定时器相关函数。
Node中的定时器相关函数主要有：
- ```void scheduleUpdate(void);```每个Node对象只要调用该函数，那么这个Node对象就会定时的每帧回调一次自己的```update(float dt)```函数。
- ```void schedule(SEL_SCHEDULE selector, float interval);```与scheduleUpdate函数功能一样，不同的是我们可以指定回调函数（通过selector指定）。也可以更加需要指定回调时间间隔。
- ```void unscheduleUpdate(void);```停止```update(float dt)```函数调度。
- ```void unschedule(SELSCHEDULE selector);```可以指定具体函数停止调度。
- ```void unscheduleAllSelectors(void);```可以停止调度。
----
#### 3.6 cocos2d-x坐标系
##### 3.6.1 UI坐标
UI坐标原点在左上角，x轴为向右为正，y轴向下为正。
Android和iOS等平台使用的视图、控件等都是遵守这个坐标系。然而Cocos2dx默认不是采用UI坐标，但有的时候也会用到UI坐标，例如在触控事件发生的时候，我们会获得一个触摸对象（Touch），触摸对象提供了很多获得位置信息的函数，如下面的代码：
```
Vec2 touchLocation2 = touch ->getLocationInView;
```
使用```getLocationInView()```函数获得触摸点坐标事实上就是UI坐标，它的坐标原点在左上角，而不是Cocos2d-x默认坐标，我们可以采用下面的语句进行转换：
```
Vec2 touchLocation2 = Director::getInstance() -> convertToGL(touchLocation);
```
通过上面的语句就可以将触摸点位置从UI位置转换为OpenGL坐标，OpenGL坐标就是Cocos2d-x默认坐标。

##### 3.6.2 OpenGL坐标
Cocos2d-x底层采用的是OpenGL渲染，因此默认坐标就是OpenGL坐标，只不过采用两维。如果不考虑z轴，OpenGL坐标的原点在左下角。

##### 3.6.3 世界坐标和模型坐标
由于OpenGL坐标又可以分为世界坐标和模型坐标，所以Cocos2d-x的坐标也有世界坐标和模型坐标。
世界坐标和模型坐标互相转换：
- ```Vec2 convertToNodeSpace(const Vec2& worldPoint);//将世界坐标转换为模型坐标```
- ```Vec2 convertToNodeSpaceAR(const Vec2& worldPoint);//将世界坐标转换为模型坐标，AR表示相对于锚点```
- ```Vec2 convertTouchToNodeSpace(Touch* touch);//将世界坐标中的触摸点转换为模型坐标```
- ```Vec2 convertTouchToNodeSpaceAR(Touch* touch);//将世界坐标中触摸点转换为模型坐标，AR表示相对于锚点```
- ```Vec2 convertToWorldSpace(const Vec2 & nodePoint);//将模型坐标转换为世界坐标```
- ```Vec2 convertToWorldSpaceAR(const Vec2& nodePoint);//将模型坐标转换为世界坐标世界坐标，AR表示相对于锚点```
----
### 第四章 游戏中的文字
---
#### 4.1 Cocos2d-x中的字符串
##### 4.1.1 使用const char*和std::string
std::string是一个类，具有面向对象的优点，而const char*没有。
初始化std::string对象：
```
std::string name = "tony";
std::string name = std::string("tony");
```
std::string转化为const char*：
```
const char* cstring = name.c_str();
```
使用std::string指针类型，但是要配合new关键字开辟空间，然后不使用时通过delete释放内存、
```
std::string* name = new std::string("tony");
...
delete name;
```
使用std::string指针对象时，可以通过下面的代码转化为const char*类型：
```
const char* cstring = name->c_str();
```
