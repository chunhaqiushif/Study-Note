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
##### 5.3.2 __Dictionary数据结构
创建__Dictionary对象：
```
static __Dictionary* create();//创建__Dictionary
static __Dictionary* createWithDictionary(__Dictionary* srcDict);//用一个已存在的__Dictionary来创建一个新的__Dictionary
static __Dictionary* createWithContentsOfFile(const char* pFileName);//从属性列表文件创建__Dictionary
```
添加元素：
(添加元素都必须是“键-值”对，“键”可以是字符串（std::string）类型或整数（signed int）类型，而“值”必须是Ref和其子类的对象指针类型。)
```
void setObject(Ref* pObject, const std::string& key);
//插入一个“键-值”对，其中pObject是“值”，key是“键”。
//如果第一次调用，__Dictionary的“键”类型是字符串型，之后就不能插入整形“键”。
//如果已存在该“键”，则旧“键-值”对会被释放和移除，被新的替代。
void setObject(Ref* pObject, intptr_t key);
//插入一个“键-值”对，其中pObject是“值”，key是“键”，intptr_t类型是signed int类型的别名，为整型。
//如果是第一次调用，__Dictionary的“键”类型是整形，之后就不能插入字符串型“键”。
//如果已存在该“键”，则旧“键-值”对会被释放和移除，被新的替代。
```
移除元素：
```
void removeObjectForKey(const std::string &key);//通过指定键移除元素
void removeObjectForKey(intptr_t key);//通过指定键移除元素
void removeObjectForKeys(__Array* pKeyArray);//通过一个__Array中键集合移除元素
void removeObjectForElement(DictElement* pElement);//通过指定元素来移除
void removeAllObject();//移除所有元素
```
查找元素：
```
Ref* objectForKey(const std::string &key);//返回指定字符串类型“键”的“值”。
Ref* objectForKey(intptr_t key);//返回指定整形“键”的“值”
const __String* valueForKey(const std::string &key);
//返回指定字符串类型“键”的“值”，返回值是__String指针类型。
//这里假定“值”是__String指针型，如果不是或未找到，则返回空串。
const __String* valueForKey(intptr_t key);
//返回指定整形“键”的“值”，返回值是__String指针类型。
//这里假定“值”是__String指针型，如果不是或未找到，则返回空串。
```
其他操作函数
```
__Array* allKeys();//返回一个包含所有“键”的“值”的__Array数据结构
unsigned int count();//返回元素个数
bool writeToFile(const char* fullPath);
//把一个__Dictionary写到一个属性列表文件中，写入的“值”要求是字符串型
```
遍历_Dictionary数据结构：
Cocos2d-x提供了一个宏：```CCDICT_FOREACH```，用来遍历__Dictionary数据结构。

##### 5.3.3 Map<K, V>数据结构
Map<K, V>是Cocos2d-x 3.x推出的字典数据结构，它也能容纳Ref类型。内存管理是由编译器自动处理的，可以不用考虑内存释放问题。性能优于__Dictionary类。
创建Map<K, V>对象：
```
Map();//默认构造函数
Map(ssize_t capacity);//创建Map，并设置容量
Map(const Map<K, T>&other);//用一个已存在的Map创建另一个Map
Map(Map<K, V>&&other);//用一个已存在的Map创建另一个Map
```
添加元素：
```
void insert(const K &key, V object);
//在Map中添加一个新元素，V必须是Ref以及子类指针类型。
```
移除元素：
```
iterator erase(const_iterator position);//指定位置移除对象，参数是迭代器，而返回值是下一个迭代器
size_t erase(const std::vector<K>&keys);//通过给定键集合移除多个元素
void clear();//移除所有元素
```
查找元素：
```
const V at(const K &key) const;//通过“键”返回“值”
V at(const K &key);//返回指定整形“键”的“值”
const_iterator find(const K &key) const;//查找Map数据结构中的对象，返回值迭代器
iterator find(const K &key);//查找Map数据结构中的对象，返回值迭代器
```
其他操作函数：
```
std::vector<K> keys();//返回所有的“键”
std::vector<K> keys(V object);//返回与对象匹配的所有“键”
ssize_t size();//返回元素个数
```
#### 5.4 Value列表数据结构——ValueVector
##### 5.4.1 ValueVector常用API
ValueVector的API就是C++标准数据结构类std::vector的API。
创建ValueVector对象：
```
ValueVector ret;//默认构造函数

//如果需要在创建时进行初始化，可以采用Lambda表达式创建ValueVector对象
auto createValueVector = [&](){
  ValueVector ret;
  ret.push_back(v1);
  ret.push_back(v2);
  ret.push_back(v3);
  return ret;
};
ValueVector vv = createValueVector();
```
访问元素：
```
Value &at(size_type n);//返回n位置的元素
Value &front();//返回第一个元素
Value &back();//返回最后一个元素
```
修改元素：
```
push_back(const ValueVector &val);//添加一个元素，val参数必须是ValueVector类型
pop_back();//删除最后一个元素
```
#### 5.5 Value字典数据结构——ValueMap和ValueMapIntKey
```
//定义
typedef std::unordered_map<std::string, Value> ValueMap;
typedef std::unordered_map<int, Value> ValueMapIntKey;
```
从定义上看，二者只能容纳Value类型的C++标准数据结构类std::unordered_map，ValueMap的“键”是std::string类型，ValueMapIntKey的“键”是int类型。与ValueVector一样，二者内存管理不采用引用计数，在使用时也不需要关心内存的释放问题。
##### 5.5.1 ValueMap和ValueMapIntKey常用API
创建ValueMap和ValueMapIntKey对象：
```
ValueMap ret;//默认构造函数

//如果需要在创建时进行初始化，可以采用Lambda表达式创建ValueMap和ValueMapIntKey对象
auto createValueMap = [&](){
  ValueMap ret;
  ret["aaa"] = v1;
  ret["aaa"] = v1;
  ret["aaa"] = v1;
  return ret;
}
ValueVector vm = createValueMap();

auto createValueMapIntKey = [&](){
  ValueMapIntKey ret;
  ret["aaa"] = v1;
  ret["aaa"] = v1;
  ret["aaa"] = v1;
  return ret;
}
ValueVector vmi = createValueMapIntKey();

//其中aaa是键，v1是值，以此类推
```
访问元素：
```
ValueMap ret;
ret.at("Mars") = Value(3000);//采用at函数访问
ret["Saturn"] = Value(6000);//采用下标访问
ret["Jupiter"] = Value(7000);//采用下标访问
```
修改元素：
```
insert(std::pair<std::string, Value>);//添加键值对
erase(const key_type &k);//根据键删除值
```
---
### 第六章 菜单
---
#### 6.2 文本菜单
文本菜单是菜单项，只能显示文本，文本菜单类包括MenuItemLabel、MenuItemFont和MenuItemAtlasFont。MenuItemLabel是个抽象类，具体使用的时候是使用MenuItemFont和MenuItemAtlasFont两个类。
文本菜单类MenuItemAtlasFont中的一个创建函数create定义如下：
```
static MenuItemFont* create(const std::string &value,//要显示的文本
  const ccMeunCallback &callback//菜单操作的回调函数指针
  )
```
文本菜单类MenuItemAtlasFont是基于图片集的文本菜单项，其中一个创建函数create定义如下：
```
static MenuItemAtlasFont* create(const std::string &value,//要显示的文本
  const std::string &charMapFile,//图片集文件
  int itemWidth,//要截取的文字在图片中的宽度
  int itemHeight,//要截取的文字在图片中的高度
  char startCharMap,//开始字符
  const ccMeunCallback &callback//菜单操作的回调函数指针
  )
```
#### 6.3 精灵菜单和图片
精灵菜单的菜单项是MenuItemSprite，图片菜单的菜单项类是MenuItemImage。由于MenuItemImage继承于MenuItemSprite，所以图片菜单也属于精灵菜单。
精灵菜单项具有精灵的特点，我们可以让精灵动起来，具体使用时是吧一个精灵放置到菜单中作为菜单项。
创建函数create定义：
```
static MenuItemSprite* create(Node* normalSprite,//菜单项正常显示时的精灵
  Node* selectedSprite,//选择菜单项时的精灵
  Node* disabledSprite,//菜单项禁用时的精灵
  const ccMenuCallback &callback;//菜单操作的回调函数指针
)
```
使用MenuItemSprite比较麻烦，在创建MenuItemSprite之前要先创建三种不同状态式的精灵（即normalSprite/selectedSprite/disabledSprite）。
MenuItemSprite还有一些create函数，在这些函数中可以省略disabledSprite参数。
如果精灵是由图片构成的，我们可以使用MenuItemImage实现与精灵菜单同样的效果。
MenuItemImage类的其中一个创造函数create如下：
```
static MenuItemImage* create(const std::string &normalImage,
//菜单项正常显示时的图片
  const std::string &selectedImage,//选择菜单项时的图片
  const std::string &disabledImage,//菜单项禁用时的图片
  const ccMenuCallback &callback//菜单操作的回调函数指针
  )
```
MenuItemImage还有一些create函数，在这些函数中可以省略disabledImage参数

#### 6.4 开关操作
开关菜单的菜单项类是MenuItemToggle，他是一种可以进行两种状态切换的菜单项。
函数创建：
```
static MenuitemToggle* createWithCallback(
  const ccMenuCallback &callback;//菜单操作的回调函数指针
  MenuItem* item,//进行切换的菜单项
  ...
  )
```
从第二个参数开始都是MenuItem类的实例对象，他们是开关菜单显示的菜单项，它们可以是文本、图片和精灵类型的菜单项，但是最后不要忘记NULL结尾。
简单形式的文本类型的开关菜单项：
```
auto toggleMenuItem = MenuItem = MenuitemToggle::createWithCallback(
  CC_CALLBACK_1(HelloWorld::menuItem1Callback, this),
  MenuItemFont::create("On"),
  MenuItemFont::create("Off"),
  NULL
  );
  Menu* mn = Menu::create(toggleMenuItem, NULL);
  this->addChild(mn);
```
---
### 第七章 精灵
---
#### 7.1 Sprite精灵类
##### 7.1.1 创建Sprite精灵对象
创建精灵对象：
```
static Sprite* create();//创建一个精灵对象，纹理等属性需要在创建后设置
static Sprite* create(const std::string &filename);//指定图片创建精灵
static Sprite* create(const std::string &filename, const Rect &rect);
//指定图片和裁剪的矩形区域来创建精灵。
static Sprite* createWithTexture(Texture2D* texture);//指定纹理创建精灵
static Sprite* createWithTexture(Texture2D* texture,
  const Rect &rect,
  bool rotated = false);
//指定纹理和裁剪的矩形区域来创建精灵，第三个参数为是否旋转纹理，默认不旋转
static Sprite* createWithSpriteFrame(SpriteFrame* pSpriteFrame);
//通过一个精灵帧对象创建另一个精灵对象
static Sprite* createWithSpriteFrame(const std::string &spriteFrameName);
//通过指定帧缓存精灵帧名创建精灵对象
```
#### 7.2 精灵的性能优化
##### 7.2.1 使用纹理图集
纹理图集（Texture Atlas）也称作精灵表（Sprite Sheet），他是把许多小的精灵图片集合到一张大图里面。
使用纹理图集（或精灵表）的优点：
- 减少文件读取次数，读取一张图片比读取一堆小文件要快。
- 减少OpenGl ES绘制调用并且加速渲染。
- 减少内存消耗。OpenGL ES 1.1仅仅能够使用2的n次幂大小的图片（即宽度或者高度是2/4/6/8/64...）。如果采用小图片OpenGL ES 1.1会分配给每个图片2的n次幂大小的空间。即使这张图片达不到这样的宽度和高度也会分配大于此图片的2的n次幂大小的空间。那么运用这种图片集的方式将会减少内存碎片。虽然在Cocos2d-x 2.0后使用了OpenGL ES 2.0后不会再分配2的几次幂的内存块了，但是减少读取次数和绘制的优势仍然存在。
- Cocos2d-x全面支持Zwoptex和TexturePacker，所以创建和使用纹理图集是很容易的。纹理图集文件```.plist```。
使用精灵表文件最简单的方式是使用Sprite的```create(const std::string &filename, const Rect &rect)```函数。

使用create：
```
auto mountain1 = Sprite::create("SpriteSheel.png", Rect(1, 1251, 934, 388));
mountain1->setAnchorPoint(Vec2::ZERO);
mountain1->setPosition(Vec2(-200, 80));
mountain1->addChild(mountain1, 0);
```
创建纹理Texture2D对象，也可以使用精灵表文件：
```
Texture2D* cache  = Director::getInstance()->getTextureCache()->
  addImage("SpirteShell.png");
auto hero1= Sprite::create();
hero1->setTexture(cache);
hero1->setTextureRect(Rect(1, 204, 393, 327));
hero1->setPosition(Vec2(800, 200));
this->addChild(hero1, 0);
```
##### 7.2.2 使用精灵帧缓存
精灵帧缓存是缓存的一种，缓存有如下几种：
- 纹理缓存（）：使用纹理缓存可以创建纹理对象。
- 精灵帧缓存（）：能够从精灵表中创建精灵帧缓存，然后再从帧缓存中获得精灵对象，反复使用精灵对象时，使用精灵帧缓存可以节省内存消耗。
- 动画缓存（）：动画缓存主要用于精灵动画，精灵动画中的每一帧是从动画缓存中获取的。

要使用精灵帧缓存，涉及的类有```SpriteFrame```和```SpriteFrameCache```。
使用SpriteFrameCache创建精灵对象的主要代码如下：
```
SpriteFrameCache::getInstance()->addSpriteFramesWithFile("SpriteSheel.plist");
auto mountain1 = SpriteFrameCache = Sprite::createWithSpriteFrameName(
  "mountain1.png");
```

#### 7.3 纹理图集制作
纹理像素的格式：
- RGBA8888：32位色，他是默认的像素格式，每个通道8位（比特），每个像素4个字节。
- BGRA8888：32位色，每个通道8位（比特），每个像素4个字节。
- RGBA4444：16位色，每个通道4位（比特），每个像素2个字节。
- RGB888：24位色，没有Alpha通道，所以没有透明度。每个通道8位（比特），每个像素3个字节。
- RGB565：16位色，没有Alpha通道，所以没有透明度。R和B通道是各5位，G通道是6.
- RGB5A1（或RGBA5551）：16位色，每个通道各4位，Alpha通道只用1位表示。
- PVRTC4：4位PVR压缩纹理格式，PVR格式是专门为iOS设备上面的PowerVR图形芯片而设计的。他们在iOS设备上非常好用，因为可以直接加载到显卡上面，而不需要经过中间的计算转化。
- PVRTC4A：具有Alpha通道的，4位PVR压缩纹理格式。
- PVRTC2：2位PVR压缩纹理格式。
- PVRTC2A：具有Alpha通道的，2位的PVR压缩纹理格式。

防抖效果：（Texture->Dithering）
可以防止“带状伪影”现象。
---
### 第八章 场景与层
---
#### 8.1 场景与层的关系
节点的层级结构中我们能够了解到场景（Scene）与层（Layer）的关系为：一个场景有n个层对应，而且层的个数至少要为1，不能为0。
编程时往往不需要子类化（编写子类）场景，而是子类化层。虽然场景和层是1：n的关系，但是通过模板生成的工程默认情况下都是1：1关系。
#### 8.2 场景切换
##### 8.2.1 场景切换相关函数
场景切换是通过导演类Director实现的，其中的相关函数如下：
```
void runWithScene(Scene* scene);
//该函数可以运行场景。只能在启动第一个场景时调用该函数，
//如果已经有一个场景运行情况下则不能调用该函数。
void replaceScene(Scene* scene);
//切换到下一个场景。用一个新的场景替换当前场景，当前场景被终端释放
void pushScene(Scene* scene);
//切换到下一个场景。将当前场景挂起放入到场景堆栈中，然后再切换到下一个场景中。
void popScene(Scene* scene);
//与pushScene配合使用，可以回到上一个场景。
void popToRootScene();
//与pushScene配合使用，可以回到根场景。
```
需要注意replaceScene和pushScene使用的区别。replaceScene会释放和销毁场景，如果需要保持原来场景的状态，replaceScene函数不适合。pushScene并不会释放和销毁场景，原来场景的状态可以保持，但是游戏中也不能出现太多的场景对象运行。
使用replaceScene代码如下：
```
auto sc = Setting::createScene();
DIrector::getInstance()->replaceScene(sc);
//其中的Setting是下一个要切换的场景。
```
使用pushScene代码如下：
```
auto sc = Setting::createScene();
Director::getInstance()->pushScene(sc);
```
从Setting场景回到上一个场景使用代码如下：
```
Director::getInstance()->popScene();
```
##### 8.2.2 场景过渡动画
场景切换时可以添加过渡动画，场景过渡动画是由TransitionScene类和它的子类展示的。
TransitionScene类的直接子类有13个，而且有些子类还有子类，全部的过渡动画类30多个。
过渡动画类的使用方式都是类似如下：
```
auto sc = Setting::createScene();
auto reScene = TransitionJumpZoom::create(1.0f, sc);
Director::getInstance()->pushScene(reScene);
```
10个有代表性的过渡动画：
- TransitionFadeTR：网格过渡动画，从左下到右上
- TransitionJumpZoom：跳动的过渡动画
- TransitionCrossFade：交叉渐变过渡动画
- TransitionMoveInL：从左边推入覆盖的过渡动画
- TransitionShrinkGrow：放缩交替的过渡动画
- TransitionRotoZoom：类似照相机镜头旋转放缩交替的过渡动画
- TransitionSlideInL：从左侧推入的过渡动画
- TransitionSplitCols：按列分割界面的过渡动画
- TransitionSplitRows：按行分割界面的过渡动画
- TransitionTurnOffTiles：生成随机瓦片方格的过渡动画

#### 8.3 场景的生命周期
一般情况下，一个场景只需要一个层。层需要子类化，编写自己层类。由于这些原因场景的生命周期就通过层的生命周期反映出来，通过重写层的生命周期函数，可以处理场景不同生命周期阶段的事件，如我们可以在层的进入函数（onEnter）中做一些初始化处理，而在层退出函数（onExit）中释放一些资源。
##### 8.3.1 生命周期函数
层（Layer）的生命周期函数如下：
```
bool init();//初始化层调用
void onEnter();//进入层时调用
void onEnterTransitionDidFinish();//进入层而且过渡动画结束时调用
void onExit();//退出层时调用
void onExitTransitionDidStart();//退出层而且开始过渡动画时调用
void clearup();//层对象被清除时调用
```
> Tips_1：层（Layer）继承于节点（Node），这些生命周期函数根本上是从Node继承而来。事实上所有Node对象（包括：场景、层、精灵等）都有这些函数，只要是子类化这些类都可以重写这些函数，来处理这些对象的不同生命周期阶段事件。

>Tips_2：在重写生命周期函数中，第一行代码应该是调用父类的函数，例如```HelloWorld::onEnter(){...}```中第一行应该是```Layer::onEnter();```函数，如果不调用父类的函数可能会导致层中动画、动作或计划无法执行。

如果HelloWorld是第一个场景，当启动HelloWorld场景的时候，它的调用顺序如下：
```
HelloWorld::init函数->HelloWorld::onEnter函数->HelloWorld::onEnterTransitionDidFinish函数
```
##### 8.3.2 多场景切换生命周期
在多个场景切换时，场景的生命周期会更加复杂。
多个场景切换时分为几种情况：
1. 使用pushScene函数实现从HelloWorld场景进入Setting场景。

  ```
  //情况1调用顺序
  1. Setting::init函数
  2. HelloWorld::onExitTransitionDidStart函数
  3. Setting::onEnter函数
  4. HelloWorld::onExit函数
  5. Setting::onEnterTransitionDidFinish函数
  ```
2. 使用replaceScene函数实现从HelloWorld场景进入Setting场景。

  ```
  //情况2调用顺序
  1. Setting::init函数
  2. HelloWorld::onExitTransitionDidStart函数
  3. Setting::onEnter函数
  4. HelloWorld::onExit函数
  5. Setting::onEnterTransitionDidFinish函数
  6. HelloWorld::cleanup函数
  ```
3. 在1.的情况下使用popScene函数实现从Setting场景回到HelloWorld场景。

  ```
  //情况3调用情况
  1. Setting::onExitTransitionDidStart函数
  2. Setting::onExit函数
  3. Setting::cleanup函数
  4. HelloWorld::onEnter函数
  5. HelloWorld::onEnterTransitionDidFinish函数
  ```
---
### 第九章 动作和动画
---
#### 9.1 基本动作
动作（Action）包括了基本动作和基本动作的组合；基本动作包括缩放、移动、旋转等动作，而且这些动作变化的速度也可以设定。
Node类有关于动作的函数如下：
```
Action* runAction(Action* action);//运行指定动作，返回值仍然是一个动作对象
void stopAction(Action* action);//停止所有动作
void stopActionByTag(int tag);//通过指定标签停止动作
void stopAllActions();//停止所有动作
```
##### 9.1.1 瞬时动作
瞬时动作就是不等待，马上执行的动作，瞬时动作的基类是ActionInstant。

##### 9.1.2 间隔动作
间隔动作执行需要一定的时间，我们可以设置duration属性来设置动作的执行时间。

##### 9.1.3 组合动作
组合动作包括以下几类：顺序、并列、有限次数重复、无限次数重复、反动作和动画。

##### 9.1.4 动作速度控制
通过ActionEase及其子类和Speed类，我们可以使精灵以非匀速或非线性速度运动。

##### 9.3.1 帧动画
帧动画就是按一定时间间隔、一定的顺序、一帧一帧地显示帧图片。在Cocos2d-x中播放帧动画涉及两个类：Animation和Animate，Animation是动画类，它保存有很多动画帧。Animate类是动作类，它继承于ActionInterval类，属于间隔动作类，它的作用是将Animation定义的动画转换成为动作进行执行，这样我们就能看到动画播放的效果了。

> 抄书人Tuple(/・ω・)|||：
第九章几乎全是实例，大家最好还是自己去买书看23333我就不贴上去了（或许以后会贴）

---
### 第十章 用户事件
---
#### 10.1 事件处理机制
在很多图形用户技术中，事件处理机制一般都有三个重要的角色：事件、事件源和事件处理者。事件源是事件发生的场所，通常就是各个视图或控件，事件处理者是接受事件并对其进行处理的一段程序。
1. 事件
- 事件类是Event，其子类有：EventTouch（触摸事件）、EventMouse（鼠标事件）、EventKeyBoard（键盘事件）、EventAcceleration（加速度事件）和EventCustom（自定义）、以及EventCustom的子类PhysicsContact（物理引擎中接触事件）。
2. 事件源
- 事件源是Cocos2d-x中的精灵、层、菜单等节点对象。
3.事件处理者
- Cocos2d-x中的事件处理者是事件监听类EventListener，其子类有：EventListenerTouchOneByOne（单点触摸事件监听器）、EventListenerTouchAllAtOnce（多点触摸事件监听器）、EventListenerKeyboard（键盘事件监听器）、EventListenerMouse（鼠标事件监听器）、EventListenerAcceleration（加速度事件监听器）EventListenerController（游戏手柄事件监听器）、EventListenerFocus（焦点事件监听器）和EventListenerCustom（自定义事件监听器），以及EventListenerCustom的子类EventListenerPhysicsContact（物理引擎中接触事件监听器）

##### 10.1.1 事件分发器
事件监听器与事件具有对应关系，如：键盘事件只能由键盘事件监听器处理，他们之间需要在程序中建立关系，这种关系的建立过程被称为“注册监听器”。Cocos2d-x提供一个事件分发器（EventDispatcher）负责管理这种关系。具体说，事件分发器负责：注册监听器、注销监听器和事件分发。
EventDispatcher类采用单例模式设计，通过```DIrector::getInstance()->getEventDIspatcher()```语句获得事件分发器对象。
EventDispatcher类中注册事件监听器到事件分发器函数如下：
```
void addEventListenerWithFixedPriority(EventListener* listener, int fixedPriority);
//指定固定的时间优先级注册监听器，事件优先级决定事件相应的优先级别，值越小优先级越高。
void addEventListenerWithSceneGraphPriority(
  EventListener* listener, Node* node);
//把精灵显示优先级作为事件优先级。参数node是要触摸精灵对象。
```
当不再进行事件响应的时候，我们应该注销事件监听器，主要的注销函数如下：
```
void removeEventListener(EventListener* listener);
//注销指定的事件监听器
void removeCustomEventListeners(const std::string &customEventName);
//住校自定义事件监听器
void removeAllEventListener();//注销所有事件监听器
//需要注意的是，使用该函数之后，菜单也不能响应事件了
//因为它也需要接受触摸事件
```
##### 10.1.2 触摸事件
理解一个触摸事件可以从时间和空间两方面考虑：
1. 触摸事件的时间方面：
触摸事件中，可以有不同的“按下”、“移动”和“抬起”等阶段，表示触摸是否刚刚开始、是否正在移动或者处于静止状态，以及何时结束，也就是手指何时从屏幕抬起。此外，触摸事件的不同阶段都可由单点触摸和多点触摸，是否支持多点触摸还要看设备和平台。
触摸事件有两个事件监听器：EventListenerTouchOneByOne和EventListenerTouchAllAtOnce，分别对应单点触摸和多点触摸。这些监听器有一些触摸事件响应属性，这些属性对应着触摸事件的不同阶段。通过设置这些属性能够实现事件与事件处理者函数的关联。
EventListenerTouchOneByOne中的触摸事件响应属性：
```
std::function<bool(Touch*, Event*)> onTouchBegin;
//当一个手指触碰屏幕时回调该属性所指定函数
//如果函数返回值为true，则可以回调后面的两个属性
//（onTouchMoved和onTouchEnd）所指定的函数，否则不回调
std::function<void(Touch*, Event*)> onTouchMoved;
//当一个手指在屏幕移动时回调该属性所指定的函数
std::function<void(Touch*, Event*)> onTouchEnded;
//当一个手指离开屏幕时回调该属性所指定的函数
std::function<void(Touch*, Event*)> onTouchCancelled;
//当单点触摸事件被取消时回调该属性所指定的函数
```
std::function是一种通用的函数封装。std::function的实例可以是任何可以调用的目标，这些目标包括函数、Lambda表达式、绑定表达式以及其他函数对象等。
EventListenerTouchAllAtOnce中的触摸事件响应属性：
```
  std::function<void(const std::vector<touch*,> &, Event *)> onTouchesBegin;
  //当多个手指触碰屏幕时回调该属性所指定的函数
  std::function<void(const std::vector<touch*,> &, Event *)> onTouchesMoved;
  //当多个手指在屏幕上移动时回调该属性所指定的函数
  std::function<void(const std::vector<touch*,> &, Event *)> onTouchesEnded;
  //当多个手指离开屏幕时回调该属性所指定的函数
  std::function<void(const std::vector<touch*,> &, Event *)> onTouchesCancelled;
  //当多点触摸事件被取消时回调该属性所指定的函数
```
使用这些属性的代码片段演示：
```
auto listener = EventListenerTouchOneByOne::create();
listener->onTouchBegin = CC_CALLBACK_2(HelloWorld::touchBegin, this);
...
bool HelloWorld::touchBegin(Touch* touch, Event* event)
{
  ...
  return false;
}
```
首先，我们需要使用```EventListenerTouchOneByOne::create()```创建单独触摸事件监听器对象。然后设置它的```listener->onTouchBegin```属性，其中```CC_CALLBACK_2(HelloWorld::touchBegin, this)```是使用CC_CALLBACK_2宏绑定回调函数，该函数是下面定义的```bool HelloWorld::touchBegin(Touch* touch, Event* event)```函数。其他触摸事件的阶段也需要采用类似的代码。

2. 触摸事件的空间方面：
空间方面就是每个触摸点（Touch）对象包含了当前位置信息，以及之前的位置信息（如果有的话）。
下面的函数时可以获得触摸点之前的位置信息：
```
Vec2 getPreviousLocationInView();//UI坐标
Vec2 getPreviousLocation();//OpenGL坐标
```
下面的函数是可以获得触摸点当前的位置信息：
```
Vec2 getLocationInView();//UI坐标
Vec2 getLocation();//OpenGl坐标
```
##### 10.1.5 键盘事件
Cocos2d-x的键盘事件与接触时间不同，他没有空间方面的信息。键盘事件不仅可以响应键盘，还可以响应设备的菜单。
键盘事件监听器是EventListenerKeyboard。
EventListenerKeyboard中键盘事件响应属性：
```
std::function<void(EventKeyboard::KeyCode, Event*)> onKeyPressed;
//当键按下时回调该属性所指定的函数
std::function<void(EventKeyboard::KeyCode, Event*)> onKeyReleased;
//当键抬起时回调该属性所指定的函数
```
我们可以使用Lambda表达式实现键盘事件处理，代码片段如下：
```
void HelloWorld::onEnter()
{
  Layer::onEnter();
  log("HelloWorld onEnter");
  auto listener = EventListenerKeyboard::create();

  listener->onKeyPressed = [ ](EventKeyboard::KeyCode keyCode, Event* event){
    log("Key with keycode %d pressd", keyCode);
  };

  listener->onKeyReleased = [ ](EventKeyboard::keyCode keyCode, Event* event){
    log("key with keycode %d", keyCode);
  };
  EventDispatcher* eventDispatcher = Director::getInstance()->getEventDispatcher();
  eventDispatcher->addEventListenerWithFixedPriority(listener, this);
}
```

##### 10.1.6 鼠标事件
鼠标事件是在Cocos2d-x 3.0之后添加的，可以在不同的平台上使用，丰富游戏用户的体验。当然也必须注意设备的支持。
鼠标事件监听器是EventListenerMouse。
EventListenerMouse中的鼠标事件响应属性：
```
std::function<void(Event* event)> onMouseDown;
//当鼠标键按下时回调该属性所指定函数
std::function<void(Event* event)> onMouseMove;
//当鼠标键移动时回调该属性所指定函数
std::function<void(Event* event)> onMouseScroll;
//当鼠标滚轮滚动时回调该属性所指定函数
std::function<void(Event* event)> onMouseUp;
//当鼠标键抬起时回调该属性所指定函数
```
可以使用Lambda表达式实现键盘事件处理。

#### 10.2 在层中进行事件处理
层是节点的派生类。我们可以在层中响应时间处理。但是层是一个非常特殊的节点，场景中的主要工作都是在层中完成的，因此Cocos2d-x中为Layer定义了一些与事件相关的函数。
Layer中单点触摸事件相关的函数：
```
bool onTouchBegin(Touch* touch, Event* event);
void onTouchCancelled(Touch* touch, Event* event);
void onTouchEnded(Touch* touch, Event* event);
void onTouchMoved(Touch* touch, Event event);
```
Layer中多点触摸事件相关函数：
```
void onTouchesBegin(const std::vector<Touch*> &touches, Event* event);
void onTouchesCancelled(const std::vector<Touch*> &touches, Event* event);
void onTouchesEnded(const std::vector<Touch*> &touches, Event* event);
void onTouchesMoved(const std::vector<Touch*> &touches, Event* event);
```
Layer中键盘事件相关的函数：
```
void onKeyPressed(EventKeyboard::KeyCode keyCode, Event* event);
void onKeyReleased(EventKeyboard::keyCode keyCode, Event* event);
```
Layer中加速度事件相关的函数：
```
void onAcceleration(Acceleration* acc, Event* event);
```

##### 10.2.1 触摸事件
层中的触摸事件使用起来比较简单，由于事件监听的对象是层，而不是层中的精灵等对象，当判断是否点击了哪个精灵对象时比较麻烦，我们需要使用Rect的containsPoint函数逐一判断每个精灵对象。
在层中的触摸事件的一般流程开发是，首先在层的h文件中定义要重写的事件相关函数，然后在cpp文件中实现。层中的事件处理比较简单，不需要注册事件监听器，不需要指定它的事件响应优先级别。这种优点也是它的缺点，在处理多个精灵的触摸事件时，就有些力不从心。

---
### 第十一章 Audio引擎
---
#### 11.1 Cocos2d-x中音频文件
##### 11.1.1 音频文件介绍
音频多媒体文件主要是存放音频数据信息，音频文件在录制的过程中把信号通过音频编码，变成音频数字信号保存到某种格式文件中。在播放过程中再对音频文件解码，解码出的信号通过扬声器等设备就可以转成音波。音频文件在编码的过程中数据量很大，所以有的文件格式对数据进行了压缩，因此音频文件可以分为：
- 无损格式是非压缩数据格式，文件很大，一般不适合移动设备。例如WAV、AU、APE等文件是此格式。
- 有损格式对数据进行了压缩，压缩后丢掉了一些数据。例如，MP3、WMA等文件是此格式。


1. WAV文件
  WAV文件是目前最流行的无损压缩格式。WAV文件的格式灵活，可以存储多种类型的音频数据。由于文件较大不适合存储容量小的移动设备。
2. MP3文件
  MP3是一种有损压缩格式，它尽可能的去掉人耳无法感觉到的部分和不敏感的部分。MP3是利用MPEG Audio Layer 3的技术，将数据以1:10甚至1:12的压缩率，压缩成容量较小的文件。由于这么高的压缩比率非常适合于存储容量小的移动设备
3. WMA文件
  WMA格式是微软发布的文件格式，也是有损压缩格式。他与MP3格式不分伯仲。在低比特率渲染情况下，WMA格式显示出比MP3更多的优点，压缩比MP3更高，音质更好。但是，在高比特率渲染情况下，MP3还是占有优势。
4. CAFF文件
  CAFF文件是苹果开发的专门用于Mac OS X和iOS系统无损压缩音频格式。它被设计来替换WAV格式。
5. AIFF文件
  AIFF文件是苹果开发的专业音频文件格式。AIFF的压缩格式是AIFF-C（或AIFC），将数据以4:1压缩率进行压缩，专门应用于Mac OS X和iOS系统。
6. MID文件
  MID文件也叫MIDI格式，是一种专业音频文件格式，允许数字合成器和其他设备交换数据。MID文件主要用于原始乐器作品、流行歌曲的业余表演、游戏音轨以及电子贺卡等。
7. Ogg文件
  Ogg文件全程OGGVoid，是一种新的音频压缩格式，类似于MP3等格式。Ogg是完全免费、开放且没有专利限制的。Ogg文件格式可以不断地进行大小和音质的改良，而不影响旧有的编码器或播放器。

##### 11.1.2 Cocos2d-x跨平台音频支持
Cocos2d-x与Cocos2d-iphone不同，他是为跨平台而设计的游戏引擎，因此也要考虑对于音频跨平台的支持。Cocos2d对于背景音乐与音效播放在不同平台支持的格式是不同的。
Cocos2s-x对于背景音乐播放各个平台格式支持如下：
- Android平台支持与android. media. MediaPlayer所支持的格式相同，android. media. MediaPlayer是Android多媒体播放类。
- iOS平台支持推荐使用MP3和CAFF格式。
- Windows平台支持MIDI、WAV、MP3格式。
Cocos2d-x对于音效播放各个平台支持如下：
- Android平台支持Ogg和WAV文件，但最好是Ogg文件；
- iOS平台支持使用CAFF格式；
- Windows平台支持MIDI和WAV文件。

#### 11.2 使用Audio引擎
Cocos2d-x提供了一个Audio引擎，Audio引擎可以独立于Cocos2d-x单独使用，Audio引擎本质上封装了OpenAL音频处理库。
OpenAL是自由软件界的跨平台音频处理库。它用于设计多通道三维位置音效，其API风格模仿自OpenGL。
具体使用的API是SimpleAudioEngine。SimpleAudioEngine有几个常用的函数：
```
void preloadBackgroundMusic(const char* pszFilePath);
//预处理背景音乐文件
void playBackgroundMusic(const char* pszFilePath, boolbLoop = false);
//播放背景音乐，参数bLoop控制是否循环播放，默认情况下为false
void stopBackgroundMusic();//停止播放背景音乐
void pauseBackgroundMusic();//暂停播放背景音乐
void resumeBackgroundMusic();//继续播放背景音乐
bool isBackgroundMusicPlaying();//判断背景音乐是否在播放
unsigned intplayEffect(const char* pszFilePath);//播放音效
void pauseEffect(unsigned int nSoundId);
//暂停播放音效，参数nSoundId是playEffect函数返回的ID
void pauseAllEffects();//暂停所有播放音效
void resumeEffect(unsigned int nSoundId);
//继续播放音效，参数nSoundId是playEffect函数返回的ID
void resumeAllEffects();//继续播放所有音效
void stopEffect(unsigned int nSoundId);
//停止播放音效，参数nSoundId是playEffect函数返回的ID
void stopAllEffects();//停止播放所有音效
void preloadEffect(const char* pszFilePath);
//预处理音效音频文件，将压缩格式的文件进行解压处理，如MP3解压为WAV
```
##### 11.2.1 音频文件的预处理
无论是播放背景音乐还是音效，在播放之前进行预处理是有必要的，这个过程是对音频文件进行解压等处理，预处理只需要在整个游戏运行过程中处理一次就可以了。如果不进行预处理，则会发现在第一次播放这个音频文件时觉得很卡，用户体验不好。
预处理有两个相关参数：preloadBackgroundMusic和preloadEffect。
下面代码是预处理背景音乐和音频：
```
//初始化背景音乐
SimpleAudioEngine::getInstance()->preloadBackgroundMusic("sound/Jazz.mp3");
//初始化音效
SimpleAudioEngine::getInstance()->preloadEffect("sound/Blip.wav");
```
这些预处理过程代码最好放置到AppDelegate文件```applicationDidFinishLaunching()```函数中。如果放置到任何一个场景层中，当进入到这个层时会比较卡。而```applicationDidFinishLaunching()```函数是游戏启动时回调。在游戏启动时，一般会有一个延迟展示，这段时间是初始化的最佳时期。
##### 11.2.2 播放背景音乐
背景音乐的播放与停止实例代码如下：
```
SimpleAudioEngine::getInstance()->playBackgroundMusic("sound/Jazz.mp3", true);
SimpleAudioEngine::getInstance()->stopBackgroundMusic("sound/Jazz.mp3");
```
关于播放背景音乐，理论上可以将播放代码如```SimpleAudioEngine::getInstance()->playBackgroundMusic("sound/Jazz.mp3", true);```放置到三个位置：
1. 放置到init函数中：
  如果前面场景中没有调用背景音乐停止语句，就可以正常播放背景音乐；但是如果前面场景层onExit函数有调用背景音乐停止语句，那么会出现背景音乐播放几秒后停止。
  参考多场景切换生命周期：
  ```
  Setting::init()函数 -> HelloWorld::onExitTransitionDidStart()函数 ->
  Setting::onEnter()函数 -> HelloWorld::onExit()函数 ->
  Setting::onEnterTransitionDidFinish()函数
  ```
  ```HelloWorld::onExit```调用是在```Setting::init```之后，这样我们在```Setting::init```中开始播放背景音乐后，过一会调用```HelloWorld::onExit```时就会停止背景音乐播放。
2. 放置到onEnter函数中：
  同1.
3. 放置到onEnterTransitionDidFinish函数中：
  该函数是在进入层而且过渡动画结束时调用，代码放到这里不用考虑前面场景是否有调用背景音乐停止语句，而且也不会先听到声音，后出现界面现象。

综上所述，能否成功播放背景音乐，前面场景是否有调用背景音乐停止语句有关，也与当前场景中播放代码在哪个函数里有关。如果前面场景没有调用背景音乐停止语句，问题也就简单了，我们可以将播放代码放置到以上任何一处，但是如果前面场景调用了背景音音乐停止语句，onEnterTransitionDidFinish函数播放背景音乐会更好一些。
#### 11.2.3 停止播放背景音乐
关于停止背景音乐播放，同样可以将停止播放代码如```SimpleAudioEngine::getInstance()->stopBackgroundMusic("sound/Jazz.mp3");```放置到三个位置：
1. onExit函数
2. onExitTransitionDidStart函数
3. cleanup函数：
  这个函数是在层对象清除时调用的，在此处停止背景音乐播放是比较好的选择。但是如果采用replaceScene函数实现从HelloWorld场景进入Setting场景情况会有些不同，这种情况下```Setting::onEnterTransitionDidFinish()```函数调用完成后会调用```HelloWorld::cleanup()```函数，因此也会导致播放背景音乐异常。

事实上，在场景过渡过程中我们不停止播放背景音乐也是一个很好地解决方案，当在下一个场景播放新的背景音乐后，前一个场景的背景音乐播放自然就会停止，因为背景音乐不能同时播放多个。

#### 11.2.4 背景音乐播放暂停与继续
背景音乐播放暂停与继续很少使用，背景音乐播放暂停与继续示例代码如下：
```
SimpleAudioEngine::getInstance()->pauseBackgroundMusic();
SimpleAudioEngine::getInstance()->resumeBackgroundMusic();
```
它们的调用一般情况下是在游戏退到后台时调用暂停函数```pauseBackgroundMusic()```，然后在回到前台时调用继续函数```resumeBackgroundMusic()```。这些代码应该放在游戏生命周期函数中，代码如下：
```
void AppDelegate::applicationDidEnterBackground(){
  Director::getInstance()->stopAnimation();
  SimpleAudioEngine::getInstance()->pauseBackgroundMusic();
}
void AppDelegate::applicationWillEnterForeground(){
  Director::getInstance()->startAnimation();
  SimpleAudioEngine::getInstance()->resumeBackgroundMusic();
}
```
函数```applicationDidEnterBackground()```是在游戏进入到后台时回调的函数，在该函数中往往需要暂停所有的背景音乐播放。而在游戏回到前台时回调```applicationWillEnterForeground()```，在该函数中往往需要继续播放背景音乐。

---
### 第十二章 粒子系统
---
#### 12.2 粒子系统基本概念
“粒子系统”是模拟自然界中的一些粒子的物理运动的效果。如烟雾、下雨、下雪、火、爆炸等。单个或几个粒子无法体现出粒子运动规律性，必须有大量的粒子才体现运行的规律。而且大量的粒子不断消失，又有大量的粒子不断产生。微观上，粒子运动是随机的；而宏观上粒子是有规律的，它们符合物理学中的“测不准原理”。

> 粒子系统暂时先不记了 ┏ (゜ω゜)=☞就这么决定了

---
### 第十三章 瓦片地图
---

啊....
啊啊啊...

总之用Tiled地图编辑器就好```_(:з」∠)_```

---
### 第十四章 物理引擎
---
#### 14.1 使用物理引擎
物理引擎能够模仿真实世界物理运动规律，使得精灵做出自由落体、抛物线运动、互相碰撞、反弹等效果。
使用物理引擎还可以进行精准的碰撞检测。碰撞检测在不使用物理引擎时，往往只是将碰撞的精灵抽象为矩形、圆形等规则的几何图形，这样算法比较简单，但是碰撞的真实效果比较差，而且自己编写时往往算法没有经过优化，性能也不是很好。物理引擎是经过优化的，所以还是建议使用已有的成熟的物理引擎。
在Cocos2d-x 3.x之后对Chipmunk引擎进行了封装，拥有了一套自己的物理引擎，它能够与Cocos2d-x结合更加紧密，也不用我们关系物理引擎的技术细节问题。
##### 14.1.1 物理引擎核心概念
核心概念主要有：
- 世界（World）：游戏中的物理世界。
- 物体（Body）：构成物理世界的基础，具有位置、旋转角度等的特性，它上面的人和粮店之间的距离都是完全不变的，他们就像钻石那样坚硬，也可以称之为刚体（rigid body）。
- 形状（Shape）：物体的形状。一个依附于物体的二维碰撞几何结构，具有摩擦和弹性等材料属性。由于物体被抽象成刚体，忽略了形状。但是物体间的摩擦和碰撞是与形状有关的，这时候需要将形状依附于物体上。
- 接触点（Contact）管理检测碰撞。
- 关节（Joint）：把两个或多个物体固定到一起的约束。
如果将物体比作人的灵魂，那么形状就是人的躯体，是内容与形式的关系。我们创建一个物体的时候，他是没有形状的。然后根据形状附着于物体上，这时候物体就会受到摩擦力、弹力等影响，碰撞检测也是与形状关系的，一物体也可以附着于多个形状。
##### 14.1.2 物理引擎与精灵关系
物理引擎本身不包括精灵，它与精灵关系之间是相互独立的，精灵不会自动的跟着物理引擎中的物体做物理运动，通常我们需要编写代码将物体与精灵连接起来，同步它们的状态。
有些游戏引擎对精灵进行了封装，使他们能够与物理引擎中的物体同步，不需要自己编写代码实现同步，Cocos2d-x 3.x中的Sprite类就实现了这个目的，再配合其他的一些封装类，使得我们在Cocos2d-x中使用物理引擎，根本不用关心它们的细节问题。
如果游戏引擎没进行这种封装，我们直接实现Box2D引擎和Chipmunk引擎，就需要将物体与精灵的状态同步放到游戏循环中。
#### 14.2 Cocos2d-x 3.x 中物理引擎封装
##### 14.2.1 Cocos2d-x 3.x 物理引擎API
在Cocos2d-x 3.x中物理世界被融入游戏引擎的场景中，我们可以指定这个场景是否使用物理引擎。为此创建类Scene增加如下函数：
```
static Scene* createWithPhysics();//创建场景对象
bool initWithPhysics();//初始化具有物理引擎场景对象
void addChildToPhysicsWorld(Node* node);//增加节点对象到物理世界
PhysicsWorld* getPhysicsWorld();//获得物理世界对象
```
Cocos2d-x 3.x在节点类Node中增加了physicsBody属性，我们可以将物理引擎中的物体添加到Node对象中。
此外，3.x中为物理引擎增加了很多类，主要如下：
- PhysicsWorld类：封装物理引擎世界。
- PhysicsBody类：封装物理引擎物体。
- PhysicsShape类：封装物理引擎形状。
- PhysicsContact类：封装物理引擎碰撞类。
- EventListenerPhysicsContact类：碰撞检测监听类。
- PhysicsJoint类：封装物理引擎关节。
其中需要重点介绍的有：
1. PhysicsShape类：
  形状类PhysicsShape是一个抽象类，他有很多子类，如：
  - PhysicsShapeCircle：圆圈
  - PhysicsShapeBox：矩形盒子
  - PhysicsShapePolygon：多边形
  - PhysicsShapeEdgeSegment：有边的线段
  - PhysicsShapeEdgeBox：有边的矩形盒子
  - PhysicsShapeEdgePolygon：有边的多边形
  - PhysicsShapeEdgeChain：有边的链形
2. EventListenerPhysicsContact类：
  EventListenerPhysicsContact中碰撞检测响应属性：
  ```
  std::function<bool(PhysicsContact &contact)> onContactBegin;
  //两个物体开始接触时会响应，但只调用一次。
  //返回false情况下后面两个属性(onContactPreSolve和onContactPostSolve)
  //所指定的函数，不调用
  std::function<bool(PhysicsContact & contact, PhysicsContactPreSolve &solve)>
  onContactPreSolve;
  //持续接触时响应，他会被多次调用。返回false情况下后面的onContactPostSolve
  //属性所指定的函数，不调用
  std::function<void(PhysicsContact &contact, const PhysicsContactPostSolve &solve)> onContactPostSolve;
  //持续接触时响应，调用完PreSolve后调用。
  std::function<void(PhysicsContact &contact)> onContactSeparate;
  //分离时响应，但只调用一次。
  ```
3. PhysicsJoint类
  关节类PhysicsJoint是一个抽象类。随着版本变化，其子类会越来越多，以PhysicsJointDistance类为例，其他关节类的创建和使用都与其相似，只是约束的形式不同。
  PhysicsJointDistance类是距离关节类，它是最简单的关节之一。两个物体上面各自有一点，两点之间的距离必须固定不变。
  创建PhysicsJointDistance的静态函数定义如下：
  ```
  static PhysicsJointDistance* construct{
    PhysicsBody* a,
    PhysicsBody* b,
    const Vec2 &anchr1;
    const Vec2 &anchr2;
  }
  ```
  其中参数a、b是两个相互约束的物体，anchr1是链接a物体的锚点，anchr2是b物体的锚点。
  注意，物理引擎关节中的锚点与Node节点中的锚点anchorPoint不同。Node的锚点是相对于未知的比例，物理引擎关节中的锚点是绝对的坐标点。

---
### 第十五章 内存管理
---
