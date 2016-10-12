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
##### 4.1.2 使用cocos2d::__String
cocos2d::__String是Cocos2d-x的一个通用字符串类，它的设计模仿了Objective-C的NNString类，这由于Cocos2d-x源自于Cocos2d-iphone，cocos2d::__String也是基于Unicode双字节编码。
```
__String类图：
Ref ← __String
```
创建其主要静态create函数如下：
```
static __String* create(const std::string &str)
static __String* createWithFormat(const char* format,...)
```
cocos2d::__String转换为const char*类型：
```
__String* name = __String::create("Hi, Tony");
const char* cstring = name->getCString();
```
const char*转换为cocos2d::__String：
```
const char* cstring = "Hi tony";
__String* ns = __String::createWithFormat("%s", cstring);
```
std::string转换为cocos2d::__String类型：
```
std::string string = "Hi,tony";
__String* ns = __String::createWithFormat("%s", string.c_str());
```
cocos2d::__String转换为int类型：
```
int num = 123;
__String* ns = __String::createWithFormat("%d", num);
int num2 = ns->intValue();
```
#### 4.2 使用标签
##### 4.2.1 使用Label类
```
Label类类图：
Ref ← Node ← Label
```
创建Label类静态create函数如下：
```
static Label* createWithSystemFont( const std::string &text,//要显示的文字
  const std::string &font,//系统字体名
  float fontSize,//字体的大小
  const SIze& dimensions = Size::ZERO,//在屏幕上占用的区域大小，可省略
  TextHAlignment hAlignment = TextHAlignment::LEFT,//文字横向对齐方式，可省略
  TextVAlignment vAlignment = TextVAlignment::TOP)//文字纵向对齐方式，可省略

static Label* createWithTTF( const std::string &text,
  const std::string &fontFile,//字体文件
  float fontSize,
  const Size &dimensions = Size::ZERO,
 TextHAlignment hAlignment = TextHAlignment::LEFT,
 TextVAlignment vAlignment = TextVAlignment::TOP
 )

static Label* createWithTTF( const TTFConfig &TTFConfig,
  const std::string &text,
  TextHAlignment alignment = TextHAlignment::LEFT,
  int maxLineWidth = 0
  )

static Label* createWithBMFont( const std::string &bmfontFilePath,//位图字体文件
  const std::string &text,
  const TextHAlignment &alignment = TextHAlignment::LEFT,//可省略
  int maxLineWidth = 0,//可省略
  const Vec2 &imageOffset = Vec2::ZERO//可省略
  )
```
其中createWithSystemFont是创建系统字体标签对象，createWithTTF是创建TTF字体标签对象，createWithBMFont是创建位图字体标签对象。
#### 4.3 位图字体制作
工具：
1. Glyph Designer
2. BMFont

---
### 第五章 Cocos2d-x中的数据结构
---
#### 5.1 Cocos2d-x中两大类——Ref和Value
在Cocos2d-x中创造了两大类：Ref和Value，Cocos2d-x中除C++以外的几乎所有类都派生自它们。
数据结构类所能容纳的数据分为：Ref和Value。
##### 5.1.1 Cocos2d-x根类——Ref
Ref是大部分Cocos2d-x对象的基类，我们常用的类Node、Director、Action、Event和SpriteFrame等。
它们可以通过静态create函数创建对象，通过这种方式创建的对象不需要管理内存。类似：
```
SpriteFrame* frame = SpriteFrame::createWithTexture(tt2d, rect);
//其中tt2d是Texture2D对象指针类型，rect是Rect类型。
```
##### 5.1.2 包装类 Value
Value类可以将int、float、double、bool、unsigned char和char*等基本数据类型包装成类，也包装一些C++标准类，例如```std::string```、```std::vector<Value>```、```std::unordered_map<std::string, Value>```和```std::unordered_map<int, Value>```。
#### 5.2 Ref列表数据结构
##### 5.2.1 __Array数据结构
__Array类在Cocos2d-x 2.x时期就是CCArray类。继承与Ref类，因此所能容纳的是Ref及其子类所创建的对象指针。
创建__Array对象：
```
static __Array* create();//创建__Array
static __Array* create(Ref* object, ...);//使用一系列Ref创建__Array
static __Array* createWithObject(Ref* object);//使用一个Ref创建__Array
static __Array* createWithCapacity(unsigned int capacity);//创建__Array，并设置容量
static __Array* createWithArray(__Array* other__Array);//用一个已经存在的__Array创建另一个__Array
static __Array* createWithContentsOfFile(const std::string &pFileName);//从属性列表文件创建__Array
```
添加元素
```
void addObject(Ref* object);//添加一个元素
void addObjectFromArray(__Array* otherArray);//把一个__Array对象中所有元素添加到当前__Array对象中
void insertObject(Ref* object, ssize_t index);//在指定位置插入元素，ssize_t是int类型别名
```
移除元素
```
void removeLastObject();//移除最后一个元素
void removeObject(Ref* object);//移除某个元素
void removeObjectAtIndex(ssize_t index);//移除一个指定位置的元素
void removeObjectInArray(__Array* otherArray);//溢出某个数组__Array对象
void removeAllObject();//移除所有元素
void fastRemoveObject(Ref* object);//快速移除某个元素，把数组的最后一个元素（数值的最后一个元素是NULL）赋值给要删除的元素，不过这会改变原有元素的顺序
void fastRemoveObjectAtIndex(ssize_t index);//快速溢出某个位置指定位置的元素，与fastRemoveObject函数类似。
```
替换和交换元素
```
void exchangeObject(Ref* object1, Ref* object2);//交换两个元素
void exchangeObjectAtIndex(ssize_t index1, ssize_t index2);//交换两个指定位置元素
void replaceObjectAtIndex(ssize_t uIndex, Ref* object);//用一个对象替代指定位置元素
```
其他操作函数
```
ssize_t count();//返回元素个数
ssize_t capacity();//返回__Array的容量
ssize_t indexOfObject(Ref* object);//返回指定Ref对象指针的位置
Ref* objectAtIndex(ssize_t index);//返回指定位置的Ref对象指针
Ref* lastObject();//返回最后一个元素
Ref* ramdomObject;//返回随机元素
bool containsObject(Ref* object);//返回某个元素是否存在于__Array数据结构中
bool isEqualToArray(__Array* pOtherArray);//判断__Array对象是否相等
void reverseObjects();//翻转__Array数据结构
```
##### 5.2.3 Vector<T>数据结构
Vector<T>是Cocos2d-x 3.x自己的列表数据结构，因此他所能容纳的是Ref及子类的实例对象指针，其中的T是模板，表示能够放入到数据结构中的类型，在Coco2d-x 3.x中T表示Ref类。其内存管理是由编译器自动处理的，可以不用考虑内存释放。性能优于__Array类，推荐使用Vector<T>类。
创建Vector对象
```
Vector();//默认的构造函数
Vector(ssize_t capacity);//创建Vector对象，并设置容量
Vector(const Vector<T>&other);//用一个已存在的Vector对象创建另一个Vector对象，其中&other是左值引用参数传递
Vector(Vector<T>&&other);//用一个已存在的Vector对象创建另一个Vector对象，其中&&other是右值引用参数传递
```
添加元素
```
//向Vector对象中添加元素都必须是Ref对象指针类型
void pushBack(T object);//添加一个元素，T表示Ref对象指针类型
void pushBack(const Vector<T>&other);//把一个Vector对象中所有元素添加到当前Vector对象中。
void insert(ssize_t index, T object);//在指定的位置插入元素，ssize_t是int类型别名
```
移除元素
```
void popBack();//
void eraseObject(T object, bool removeAll = false);//移除某个元素
iterator erase(iterator position);//指定位置移除对象，参数是迭代器，而返回值是下一个迭代器
iterator erase(iterator first, iterator last);//指定移除对象范围（first~last），参数是迭代器，而返回值是下一个迭代器
void clear();//移除所有元素
```
替换和交换元素
```
void swap(T object1, T object2);//交换两个元素
void swap(ssize_t index1, ssize_t index2);//交换两个指定位置的元素
void replace(ssize_t index, T object);//用一个对象替代指定位置元素
```
查找操作
```
iterator find(T object);//查找Vector数据结构中的对象，返回值迭代器
T at(ssize_t index);//根据索引位置返回Vector数据结构中的元素
T front();//返回第一个元素
T back();//返回最后一个元素
T getRamdomObject();//返回随机元素
bool contains(T object);//返回某个元素是否存在数据结构中
ssize_t getIndex(T object);//返回指定对象的位置
```
其他操作函数
```
ssize_t size();//返回元素个数
ssize_t capacity();//返回Vector的容量
```
