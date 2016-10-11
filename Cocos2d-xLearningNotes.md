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
