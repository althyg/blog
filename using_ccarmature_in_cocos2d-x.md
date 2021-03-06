[在cocos2d-x中使用CCArmature实现骨骼动画](http://zengrong.net/post/1911.htm)

using CCArmature in cocos2d-x.

* <span style="color:red;">2014-07-15更新：</span>更新 在 quick-cocos2d-x 中使用 DragonBonesCPP
* <span style="color:red;">2014-04-28更新：</span>更新 DragonBones 官方C++版本 for cocos2d-x
* <span style="color:red;">2013-11-19更新：</span>更新2.2.0bug处理情况
* <span style="color:red;">2013-10-15更新：</span>更新2.2.0bug处理情况
* <span style="color:red;">2013-10-12更新：</span>加入Cocos2d-x 2.2.0 的bug
* <span style="color:red;">2013-10-11更新：</span>加入CocoStudio动画编辑器导入 .fla 文件注意事项
<hr>

## 重要更新 2014-07-15

[在 quick-cocos2d-x 中使用 DragonBonesCPP][quick]

## 重要更新 2014-04-28

DragonBones 开发组已经对 DragonBones 进行了官方的 C++ 移植，很多以前我提到的[问题][dbtag]都不复存在了。详情看这里 [DragonBones 官方C++版本 for cocos2d-x][dbcpp] 。

## 基础知识

要看懂本文，你需要了解骨骼动画（或称关节动画）是什么，以及CCArmature 和 DragonBones是什么。下面提供了一些资料：

* [Skeletal Animation (Wikipedia en)][skeletalani]
* [DragonBones 2.1快速入门指南][dragonbonesstarted]
* [Skeletal Animation (cocos2d-x wiki en)][skeletalanicocos2d]
* [骨骼动画详解 (泰然网)][skeletalanicocos2d]
* [DragonBones 官方C++版本 for cocos2d-x][dbcpp]

注意，本文讲解的是 cocos2d-x 官方版本中自带的 CCArmature 库的使用。这个库是 DragonBones AS3 库的一个不完全移植版，它的官方骨骼动画制作工具是 Cocostudio(CCS)，而不是Flash。

因此，使用Flash制作的骨骼动画，可能会被CCArmaue不完全支持。尤其是在CCArmature针对CCS做了那么多修改和优化之后。

用过CCS的人都知道，这个编辑器超级难用。很难说服美术GGMM去学习这么难用且难看的软件。对于只愿意使用熟悉的工具的美术GGMM来说，或许Flash就是唯一适合的。

To flash veteran: do you remember the 'Moho'?<!--more-->

## 不要使用官方版本的DragonBones Design Panel

在写这篇文章的时候，DragonBones的官方版本为v2.3。cocos2d-x的稳定版本为2.1.5。

为了使用cocos2d-x，我们需要选择 Zip(XML and PNGs) 的方式，将图像文件导出为独立的图像帧加上XML格式的元数据文件。官方版本的DragonBones Desigin Panel，会将元数据分成texture.xml和skeleton.xml两个文件，而cocos2d-x目前不支持这种情况。

因此，我们需要使用修改过的DragonBones Design Panel插件。在[CocoStudio的官方论坛中提供了一个这样的插件][cocostudiodl]，版本是2.0。但这个插件有bug。

因此，我在2.2的基础上重新打包了插件，并修复了bug。详情看这里：[cocos2d-x专用的DragonBones2.2][db224cocos2dx] 。

使用这个版本的插件，在导出图像文件的时候，会将texture.xml和skeleton.xml文件合并成1个，同时会修改元数据中的部分格式，使其满足cocos2d-x CCArmature 的解析库要求。

**注意：下面提到DragonBones的时候，均指这个修改过的插件。**

## DragonBones Desigin Panel输出的图像数据可以导入CocoStudio Action编辑器

可以使用CocoStudio的Action编辑器将DragonBones Design Panel输出的图片导入，然后重新输出成Cocos2d-x支持的格式。这种格式包含一个把碎图拼接好的png文件，一个plist文件和一个json文件。

如何进行上面的导入操作？可以看这个视频：[flash插件 DragonBone Design Panel导出以及CocoStudio动画编辑器的导入][input2cocostudio]

使用CocoStudio的Action编辑器导出的格式有什么优势呢？

* DragonBones输出的png是碎图，而CocoStudio Action编辑器将碎图拼接成大图了；
* plist文件是png碎图拼接的metadata；
* json文件是骨骼和动画信息。

但是，使用CocoStudio Action编辑器有几个问题：

* 莫名其妙的崩溃。在导入的DragonBones文件中包含空格、中文等内容的时候，编辑器经常直接退出，让人较难判断原因；
* 不支持DragonBones的层级关系。如果在DragonBones中设计了层级关系，那么CocoStudio无法识别，骨骼会发生很大的错位；
* CocoStudio Action编辑器不稳定。我在0.3.0.0版本中，可以导入DragonBones格式，但使用0.3.2.0，又不能导入同样的格式了，编辑器直接退出。

## 为什么不用CocoStudio

既然导入有这样那样的问题，那么直接用CocoStudio做骨骼动画好了，干吗还要用DragonBones Design Panel？

DragonBones Design Panel有如下优势：

* DragonBones的骨骼动画实现起来非常非常容易，在时间轴上放好影片剪辑就行了；
* 大多数做动画的同学都熟悉Flash，但极少知道CocoStudio，谁都愿意用自己熟悉的软件；
* Flash和DragonBones Design Panel的操作体验优于CocoStudio太多。

加上上面提到的不稳定原因，我也无法说服自己使用CocoStudio Action编辑器，更别说把它交给美术MM了。

更何况，我们根本不必把DragonBones Design Panel生成的文件导入CocoStudio！cocos2d-x能直接支持DragonBones Desian Panel生成的文件！

如果你还是希望用CocoStudo来做骨骼动画，可以参考这篇文章：[使用 CocoStudio 创建 Cocos2d-x 序列帧和骨骼动画][cocostudioskeleton]。

**2013-10-11 update** CocoStudio升级到1.0之后，已经支持导入fla文件，但这个导入是有限制的：

1. 需要将 想要导入到动画编辑器的元件放入到主场景中
2. 暂时不支持导入矢量图形，可以右键矢量图形 装换为位图
3. 暂不支持嵌套元件的元件作为骨骼部件 

[这篇帖子详细介绍了这个限制][importfla] ，也有允许导入的文件范例。

## 生成cocos2d-x支持的文件格式

上面已经提到，使用DragonBones可以生成一堆碎图文件和一个xml文件。我们首先要做的，就是把这堆碎图文件拼成一张大图。cocos2d-x支持plist格式(基于XML)的元数据。

当然，如果你使用我修改过的 [cocos2d-x专用的DragonBones2.2][db224cocos2dx]， 就不必进行下面的拼合工作，它会自动帮你生成png/xml/plist文件。

你也可以选择 [Sprite Packer(免费)][spritepacker] 或者 [Texture Packer][texturepacker] 来做拼合碎图以及生成plist文件的工作。[Sprite Sheet Editor][sse] 正在准备支持cocos2d的plist文件格式。

拼合成功后，可以将碎图删除，现在我们有3个文件（为了方便描述，这里假设主文件名均为skeleton）：

1. 拼合后的大的png文件 skeleton.png；
2. plist元数据文件 skeleton.plist；
3. xml骨骼动画数据文件 skeleton.xml。

再次强调，这里让主文件相同只是为了方便描述，实际使用的时候，主文件不必相同。

但是（为什么非要有但是呢？），你不认为文件名相同更方便人类阅读么？

我先来说一下 skeleton.xml 的内部结构吧。下面是我用 DragonBones 官方提供的 Dragon.fla 生成的xml文件……呃……的一部分。

<pre lang="XML">
<skeleton version="2.1" frameRate="24" name="Dragon">
	<armatures>
		<armature name="Dragon">
			<!-- 这里是一坨b 那啥~ b标签（表想不正……） -->
		</armature>
	</armatures>
	<animations>
		<animation name="Dragon">
			<!-- 这里是一坨mov -->
		</animation>
	</animations>
	<TextureAtlas name="Dragon" height="512" width="512">
		<!-- 这里是一坨SubTexture -->
	</TextureAtlas>
</skeleton>
</pre>

好了，1和2不必再检查了。但 skeleton.xml 必须检查。如果你不希望和我一样耗费一下午来猜谜的话，就记住下面几点吧：

1. 最新的测试标明，armatures是允许有多个子标签的，只要与animations中的子标签对应即可。<del datetime="2013-08-26T06:59:28+00:00">armature标签只允许有一个。你生成的xml文件中，可能由于FLA制作的问题，在armatures下面有多个armature，这是绝对不行DI。cocos2d-x碰到这种情况会直接异常没商量。所以，留一个最终正确的吧！</del>
2. armature和animation的name属性必须完全相同，这个名称将是cocos2d-x中最终使用的名称。
3. TextureAtlas的name属性和skeleton的name属性就无所谓啦，可以随便填了。
4. 还是有必要再罗嗦一遍，各种name不要用中文，不要加空格，不要用特殊字符……grumble,grumble……

话说，为什么生成的xml文件中会有多个armature呢？借势淫威……你的FLA库中的某个MovieClip中的第一层中包含label！  

我们知道（我不知道你知不知道，你知道你就是我们知道中的我们，你不知道你就不是我们知道中的我们），DragonBones会将MovieClip第一层的label当作骨骼动画中的不同动作。如果你某个不开眼的MovieClip中莫名其妙的加了一个不知所谓的label，而且你这个MovieClip又被制作动画的那个主MovieClip使用了，那么这个带有label的MovieClip也会被作为armature输出。

## 使用CCArmature包实现骨骼动画

终于特码嘚进入代码阶段了，我快要累死了。

CCArmature并不是cocos2d-x核心包的内容，你可以在 cocos2d-x/extensions 中找到它。

在头文件中，需要include CCArmature包的所有内容。我不明白为什么 cocos2d-x 开发组不把这些包含文件都放到 cocos-ext.h 中去。毕竟 spine 都被放进去了啊。难道是不稳定？不敢再往下想了，一定有阴谋，借势个阴谋……

<pre lang="CPP">
#include "cocos2d.h"
#include "cocos-ext.h"
#include "VisibleRect.h"

#include "CCArmature/CCArmature.h"
#include "CCArmature/CCBone.h"
#include "CCArmature/animation/CCArmatureAnimation.h"
#include "CCArmature/datas/CCDatas.h"
#include "CCArmature/display/CCBatchNode.h"
#include "CCArmature/display/CCDecorativeDisplay.h"
#include "CCArmature/display/CCDisplayManager.h"
#include "CCArmature/display/CCSkin.h"
#include "CCArmature/physics/CCColliderDetector.h"
#include "CCArmature/physics/CCPhysicsWorld.h"
#include "CCArmature/utils/CCArmatureDataManager.h"
#include "CCArmature/utils/CCConstValue.h"
#include "CCArmature/utils/CCDataReaderHelper.h"
#include "CCArmature/utils/CCTweenFunction.h"
#include "CCArmature/external_tool/sigslot.h"
</pre>

**2013-10-12 update** 现在阴谋终于展现了，原来cocos2d-x 2.2.0 把 CCArmature 整合进入了 `extensions/CocoStudio` 包。下面的内容出现在 `cocos-ext.h` 中：

<pre lang="CPP">
#include "CocoStudio/Armature/CCArmature.h"
#include "CocoStudio/Armature/CCBone.h"
#include "CocoStudio/Armature/animation/CCArmatureAnimation.h"
#include "CocoStudio/Armature/datas/CCDatas.h"
#include "CocoStudio/Armature/display/CCBatchNode.h"
#include "CocoStudio/Armature/display/CCDecorativeDisplay.h"
#include "CocoStudio/Armature/display/CCDisplayManager.h"
#include "CocoStudio/Armature/display/CCSkin.h"
#include "CocoStudio/Armature/physics/CCColliderDetector.h"
#include "CocoStudio/Armature/utils/CCArmatureDataManager.h"
#include "CocoStudio/Armature/utils/CCDataReaderHelper.h"
#include "CocoStudio/Armature/utils/CCTweenFunction.h"
#include "CocoStudio/Armature/external_tool/sigslot.h"
</pre>

那么，如果你使用cocos2d-x 2.2.0（或以上），只需要 `#include "cocos-ext.h"` 即可了。

然后：

* 载入资源、创建动画、播放第一个动画。
* VisibleRect这个类可以在 TestCpp 范例中找到。
* 我写得简单，是因为我只说重点和易错的地方。
* 埋怨我写的简单的，可以直接查看 TestCpp/ExtensionsTest/ArmatureTest 范例，那个详细得令人发指。

**2013-10-12 update** 在 cocos2d-x 2.2.0 中，应该查看 ExtensionsTest/CocoStudioArmatureTest 范例。

<pre lang="CPP">
CCArmatureDataManager::sharedArmatureDataManager()->addArmatureFileInfo("skeleton.png", "skeleton.plist", "skeleton.xml");
CCArmature* __armature = CCArmature::create("Dragon");
__armature->getAnimation()->playByIndex(0);
__armature->setPosition(VisibleRect::center().x, VisibleRect::center().y*0.3f);
addChild(__armature)
</pre>

## Cocos2d-x 2.2.0 在 CCArmature 上的BUG

比较 Cocos2d-x 2.1.5 和 Cocos2d-2.2.0，它们的CCArmature播放效果不同。

原本在2.1.5上非常流畅的Armature的骨骼动画，在2.2.0上会出现卡顿，以及动作不协调等情况。这不是帧率的问题，应该是在修改的过程中，Armature的Tween出了问题。

我分析出现这个BUG的原因应该是这样的：

* CCArmature其实是基于DragonBones2.1的库移植而来，就是用C++把ActionScript3代码库重写了一遍；
* 现在触控传媒希望大力推进CocoStudio的使用（目前是lite版，可能以后会出收费版），让CocoStudio去支持CCArmature骨骼动画，因此不断对CCArmature进行改进以支持CocoStudio生成的JSON文件格式；
* 在改进的过程中，可能因为某些失误，导致对原来的DragonBones格式的支持出现了问题；
* 由于这次改进仅针对CocoStudio导出的JSON格式，而没有测试DragonBones生成的格式，导致这个BUG在2.2.0发布的时候也没有得到解决；
* cocos2d-x在各种大会上说支持Flash，让Flash成为cocos2d-x的编辑器，其实团队内并不是真正重视Flash，或者并没有安排Flash的相关测试；
* 对于Cocos2d-x的使用者来说，深入了解Flash的可能比较少，更少人知道CCArmature其实是来自DragonBones，使用DragonBones的就少得可怜。再加上网上的资源匮乏，旧的、被转帖的教程泛滥，描述正确且准确的教程缺失，这个BUG就很难发现；

**以上全是我的猜测。**

但即使是官方的TestCPP，如果测试DragonBones Design Panel导出的Dragon那个Armature动画，也是能看出问题的。下面是动画，请注意尾部顶端、右臂以及腿的动作区别（这两个gif文件很大，要稍微等一会儿才会播放流畅）：

*For v2.2.0 testcpp/ExtensionsTest/CocoStudioArmatureTest*

![](http://file.zengrong.net/dragon220.gif)

*For v2.1.5 testcpp/ExtensionsTest/ArmatureTest*

![](http://file.zengrong.net/dragon215.gif)

这个BUG我已经在 [Cocos2d-x官网提出 bug#2887][bug220] ，不过开发团队似乎只测试了CocoStudio导出的格式情况，并且认为并无问题。<del>我补发了TestCpp的测试视频，官方并未回复。</del>

* **2013-10-15 update** 根据开发团队的最新回复，此BUG已经被确认，并指定目标版本为3.0alpha-1。看来要等一段时间了。在官方解决这个BUG之前，我只能使用2.1.5版本。
* **2013-11-19 update** 开发团队不认为这是个bug，而是认为DragonBones官方提供的fla demo有问题，必须按照他们要求的格式修改，才可以被2.2.0支持。说实话，辗转这么长时间，花精力提供资料和截图，却得到这样的回复我挺无语的。不过目前这个bug已经关闭，显然是开发团队认为这就是正确的解决之道。

关于开发团队对于CCArmature这个DragonBones移植版的态度，我不好多说什么。毕竟大家都是要吃饭的，把DragonBones移植过去，然后搞自己的一套东西免费开发者使用，本来也是为了开发者好。不过我应该是不会再用CocoSudio这个软件了，cocos2d-x也不会升级到2.2。

[bug220]: http://www.cocos2d-x.org/issues/2887
[skeletalani]: http://en.wikipedia.org/wiki/Skeletal_animation
[dragonbonesstarted]: http://dragonbones.github.io/getting_started_cn.html
[skeletalanicocos2d]: http://www.cocos2d-x.org/projects/cocos2d-x/wiki/Skeletal_Animation
[skeletalanicocos2dcn]: http://www.ityran.com/archives/3446
[cocostudiodl]: http://bbs.cocostudio.org/forum.php?mod=viewthread&tid=4699&extra=page%3D1
[input2cocostudio]: http://v.youku.com/v_show/id_XNTU4MDY0NTU2.html
[cocostudioskeleton]: http://www.ityran.com/archives/4386
[spritepacker]: http://spritepacker.kernys.net/
[texturepacker]: http://www.texturepacker.com/
[sse]: http://zengrong.net/sprite_sheet_editor
[db224cocos2dx]: http://zengrong.net/post/1915.htm
[importfla]: http://www.cocoachina.com/bbs/read.php?tid=160314
[dbtag]: http://zengrong.net/post/tag/dragonbones
[dbcpp]: http://zengrong.net/post/2106.htm)
[quick]: http://zengrong.net/post/2133.htm
