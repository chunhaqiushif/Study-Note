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
static MenuItemImage* create(const std::string &normalImage,//菜单项正常显示时的图片
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
