---
layout:     post
title:      阿里lowcode-engine浅析
subtitle:   低代码
date:       2025-02-07
author:     hdj
header-img: img/bgs/girl-3.jpg
catalog: true
categories : [ts]

tags:
- 低代码
---

**阿里lowcode-engine浅析**

**前言**

目前租户端是以数据模型为驱动的低代码平台，通过预置对象和用户自定义对象配合流程编排，可以满足大部分的业务场景，复杂的业务场景需要我们自己去开发相关的低代码模块，若是能够提供在线扩展低代码模块的能力给客户进行二次开发，定制化自己的业务，既能够减轻我们自身开发人员的负担，亦能够为客户提供更开放、敏捷、自由的低代码服务，满足企业开箱即用的个性化需求，基于此调研了一下阿里开源的低代码引擎--lowcode-engine。

**低代码引擎相关名词 ：**

**物料**：前端的一些组件、区块、模板、业务组件、源码组件的统称；

**Schema**：一种用来描述某种标准规范的JSON数据格式；

**协议：**一套面向开发者的 Schema
规范，用于规范化低代码设计器产出的结果以及接入物料的规范；

**一、基于协议的低代码引擎**

**1.低代码引擎定义**

lowcode-engine是一款为低代码平台开发者提供的，具备强大扩展能力的低代码研发框架，使用者只需要基于低代码引擎便可以快速定制符合自己业务需求的低代码平台。

**2.适用场景**

当业务有独特的需求，市面上的低代码平台都不满足需求，需要打造一款新的低代码平台，或需要将低代码平台的研发能力集成到已有系统中时，可以使用低代码引擎可以在极短时间内完成低代码设计器的开发，降低了低代码研发平台的研发难度和成本，根据定制化程度的不同可以将工作量由传统的几十人/月压缩到几人/月。

**3.协议规范**

**3.1 低代码基础协议**

[《低代码引擎物料协议规范》](https://lowcode-engine.cn/material):低代码引擎通过此规范渲染物料组件内容以及属性配置。

[《低代码引擎资产包协议规范》](https://lowcode-engine.cn/assets):资产包是物料包的集合，通常会把多个物料打包成资产包的形式给低代码引擎用；

[《低代码引擎搭建协议规范》](https://lowcode-engine.cn/lowcode)：搭建(编排)的本质是通过用户的拖拉拽产出这份协议，通过这份协议渲染到各个平台之上；

以物料协议为例:
协议中的每个字段定义了不同的含义，形成统一的标准，可以将其映射到各个平台，引擎通过解析这一份协议渲染成相应的界面、处理其中的逻辑，同时根据协议亦能够将其转换为真实的代码。

```Plaintext
{
"version": "1.0.0",
"componentsMap": [{ }],
"componentsTree": [{ //
低代码业务组件树，顶层由低代码业务组件容器包裹；
"componentName": "Button", // 低代码业务组件容器组件名
"fileName": "ButtonComp", //
低代码业务组件文件名，同时会将首字母大写，作为低代码业务组件名
"props": { // 定义属性
color: 'red'
}, // 一般不定义，如果有数据用于模拟外部传入的属性值
"css": "body {font-size: 12px;}", // 组件样式
"state": { // 组件状态
"label": "搜索",
},
"static": {}, // 用于定义自定组件的 static 属性
"defaultProps": { // 默认 props：
选填仅用于定义低代码业务组件的默认属性固定对象
"name": "xxx"
},
"children": [] // 子组件
}],
"i18n": { }
}

```

**3.2 为什么需要协议**

目前市面上流行的众多低代码平台所面向的业务场景不同，各个低代码平台设计的方向也大体不同，但其**物料**、**编排**、**渲染**等核心能力是能够进行复用的，基于此lowcode-engine
将低代码能力下沉，由阿里前端委员会牵头在低代码领域设计了一套协议标准，基于这套标准各个不同的低代码平台产品理论上可以实现互通，从而实现打通物料孤岛、建立统一的低代码生态。

**3.3 定义协议的优势**

由于各个低代码平台所面向的业务场景不同，其物料之间及其生态能力只能作用于自身不能共通，每新增一个低代码平台都要从0开始构建底层基础设施，不同团队和业务部门重复构建造成了极大的资源浪费，为此建立统一的低代码领域标准,基于定义的协议标准，我们可以统一业务组件、区块、模板等各类物料的标准，打破物料孤岛，实现物料之间的流通，避免了低水平重复建设，降低各业务场景中低代码平台的建设成本，提升低代码体系中物料、插件、解决方案、产物等的可流通性。

**二、lowcode-engine核心架构**

**1.MonoRepo代码管理**

作为一个开发者接触新的开源项目时首先关注的是其源码的目录结构，目录结构往往展示了项目的架构设计。使用
git clone <https://github.com/alibaba/lowcode-engine>
下载源代码之后，会发现[lowcode-engine](https://lowcode-engine.cn/)
是基于[lerna](https://lerna.js.org/)管理的单仓库多模块的管理模式，便于代码重用和调试代码，内部脚本是基于阿里内部的[build-scripts](https://github.com/ice-lab/build-scripts)脚本进行构建，基于这套管理模式在主包版本更新的时候，其他相关模块可以方便地进行同步更新,利于管理各个模块的复用以及发布，观察其代码的目录结构我们可以反推lowcdoe-engine的内核组成以及架构模式。

![](media/image1.jpeg){width="5.75in" height="2.6354166666666665in"}

**2.架构模块划分**

**2.1引擎内核**

![](media/image2.png){width="5.75in" height="3.2291666666666665in"}

**入料模块：material-parser负责物料解析，按照《物料描述协议》进行描述，将描述后的数据通过引擎
API 注册后，在编辑器中使用.**

**编排模块：engine,designer,editor-core,editor-skeleton,plugin-designer,plugin-outline-pane,shell,types,utils负责将编辑器中的所有物料，进行布局设置、组件
增删查该操作、以及 JS/CSS编写/逻辑编排转换定义的schema。**

**渲染模块：render-core,react-render，react-simulator-renderer，rax-renderer,rax-simulator-renderer,
将编排生成的页面描述结构渲染成面向用户的可视化界面。**

**出码模块：modules/code-generator
负责根据将生成的将页面描述结构schema解析和转换成应用代码。**

**2.2 分层架构**

![](media/image3.png){width="5.75in" height="3.2291666666666665in"}

自下而上分别是协议 - 引擎 - 生态 - 平台：

底层协议栈定义的是标准，**标准的统一让上层产物的互通成为可能**，适用于PC，移动，app等平台

引擎是对协议的实现，并通过 API 去生产和消费协议，其主要着重于低
代码平台的最小最核心的能力，即**入料、编排、渲染和出码**的功能**。**

基于低代码引擎和它提供的扩展能力，我们在其能力上层扩展了插件生态、物料生态、设置器生态
和支撑生态开发的工具链体系。

最后通过低代码引擎以及生态中的产品组合来衔接形成满足其需求的低代码平台，即宜搭、数据平

台 A、表单平台 B 等。

**每一层都明确自身的定位，各司其职，协议不会去思考引擎如何实现，引擎也不会实现具体上层平台功能，上层平台的定制化均通过插件来实现，这些理念将会贯穿lowcode-engine体系设计、实现的过程。**

**3.微内核架构--强大的扩展能力**

低代码引擎属于微内核架构，一开始的设计理念就是"**最小内核，最强生态**",开发者可以通过插件对引擎进行全方位的能力扩展。通过编写精简的plugin
的方式来添加更多丰富的功能,使得开发人员在不修改引擎源代码的情况下、通过开发插件的形式对引擎进行扩展。

这种插件机制可扩展的区域通常都包括左侧功能面板，中间可拖拽画布，右侧属性设置面板，顶部设置面板，工具栏面板五大区域，lowcode-engine强大的插件机制可以让用户扩展这些区域的内容，完成定制化低代码平台的开发,其低代码引擎孵化的低代码平台可以用以下的公式来理解：

**低代码设计器 = 低代码引擎 + 插件 * n + 物料 * n + 设置器 * n**

![](media/image4.png){width="5.75in" height="3.2291666666666665in"}

1）画布(MainArea)：用户可视化设计和配置的主要区域，一般融合了产品的渲染、拖拽、选择等一

系列可视化编辑的操作和功能。

2）顶部面板(TopArea)：位于顶部的面板，一般放置页面级的操作，如保存、预览。

3）左侧功能面板(LeftArea)：位于左侧的功能面板，用作为编辑器增加扩展功能模块的面板。这类面板

通常会提供一个图标，并在点击图标之后在左侧唤出一个配置面板。

4）右侧设置面板(RightArea)：位于右侧的设置面板，通常用作组件的属性配置面板。

5）顶部工具栏(ToolBar)：位于画布的顶部，一般放置画布级的快捷操作，在关系图、流程图编排的场景较为常见。

基本上低代码平台都由低代码引擎、设计器插件、物料、设置器等功能组合而来的，而其中对于lowcode-engine来说每一项都是可以进行扩展开发的。

**4.丰富的系统API**

当我们自定义插件的时候总要去调用编辑器或者监控某些事件进行自定义操作，这时就需要调用低代码引擎提供的API能力，我们可以在插件中调用API定制面板，操作节点树、定制节点的扩展操作、操作物料模型、绑定快捷键、设定画布大小、定制拖拽行为等等。

低代码引擎提供了[9大类API](https://lowcode-engine.cn/docV2/vlmeme)，根据其作用的对象以及功能可以划归为3大类，分别是组件系统，对象系统和事件系统：

**组件系统**是指[skeleton（面板）](https://www.yuque.com/lce/doc/bv3und)、[material（物料）](https://www.yuque.com/lce/doc/mu7lml)、[setters（设置器）](https://www.yuque.com/lce/doc/xwnsu3)等跟组件开发相关的模块。

**事件系统**是指[event（事件）](https://www.yuque.com/lce/doc/qrotb6)、[hotkey（快捷键）](https://www.yuque.com/lce/doc/dne3ry)等跟内外部事件相关的模块。

**对象系统**就是除了上述的模块的其他模块，比如[project（模型）](https://www.yuque.com/lce/doc/dcrbff)，就是对UI可视化搭建的面向对象建模，用于获取构建出的文档模型的节点。

![](media/image5.png){width="5.75in" height="6.25in"}

**三、基于lowcode-demo的应用实战**

lowcode-engine是一个最基础的协议+引擎的组合，只是帮我们把核心的入料、编排、渲染，物料拖拽进行了统一的规范，要在其基础上进行扩展才能满足我们的业务需求的低代码平台，以下是我根据其官方文档实现了一个简单的从登录到通用列表页的[demo](http://192.168.1.104:5556/?page=login)。

**1. lowcode-demo文件目录**

下载demo源码，之后会发现里面引入了"@alilc/lowcode-plugin-XXX"之类的插件开发，我们完全可以实现自定义的插件通过npm引入，同样也是通过build-scripts那就进行启动编译

```
JavaScript
git clone <https://github.com/alibaba/lowcode-demo>

yarn install

npm run start

```

![](media/image6.jpeg){width="5.75in" height="2.0833333333333335in"}

**2. 生态扩展**

[物料扩展](https://www.yuque.com/lce/doc/funcv8)

扩展低代码资产包asset.json

以组件库的形式扩展组件包，自建物料库，将打包后的json文件放入到项目中即可

```
JavaScript
npm init @alilc/element your-material-name. // 初始化组件库名

cd your-material-name

npm install

# 启动 lowcode 环境进行调试预览
npm run lowcode:dev

# 构建低代码产物
npm run lowcode:build

# 发布
npm publish

```

[插件扩展](https://www.yuque.com/lce/doc/funcv8)

为面板增加自定义操作

```
TypeScript
// /dj/plugins.tsx
import {skeleton} from "@alilc/lowcode-engine";
// 注册dj面板
skeleton.add({
area: 'leftArea',
type: 'PanelDock',
name: 'dj-test',
content: DjTest,
contentProps: {
logo:
'https://img.alicdn.com/imgextra/i4/O1CN013w2bmQ25WAIha4Hx9_!!6000000007533-55-tps-137-26.svg',
href: 'https://lowcode-engine.cn',
},
props: {
align: 'top',
icon: 'zujianku',
description: 'dj-test',
},
});

// DJtest.tsx
import React from 'react';
import './index.scss';
import { PluginProps } from '@alilc/lowcode-types';

export interface IProps {
logo?: string;
href?: string;
text?: string;
}

const DjTest: React.FC<IProps & PluginProps> = (props): React.ReactElement => {
return (
<div className="lowcode-dj-plugin">
this is dj plugin --- {props.text}
</div>
);
};

export default DjTest;

```

[设置器扩展](https://www.yuque.com/lce/doc/cl03wo_nmhznb)

```TypeScript
// DJStringSetter.tsx
import React, { PureComponent } from 'react'; 
import classNames from 'classnames';
import { Input } from "@alifd/next";
// import { event } from '@alilc/lowcode-engine';

interface DJStringSetterProps {
// 当前值
value: string;
// 默认值
defaultValue: string;
// setter唯一输出
onChange: (val: string) => void;
// DJStringSetter 特殊配置
placeholder: string;
}
class DJStringSetter extends PureComponent<any, any> {
// 声明Setter的title
static displayName = 'DJStringSetter';

render() {
const { onChange, value, placeholder } = this.props;
console.error('DJStringSetter render,value',value)

return (
<Input
value={value}
placeholder={placeholder || ""}
onChange={(val: any) => onChange(val)}
></Input>
);
}

}

export default DJStringSetter;

// plugins.tsx
const customSetter = (ctx: ILowCodePluginContext) => {
return {
name: '___registerCustomSetter___',
async init() {
const { setters } = ctx;
// todo 自定义dj-setter
setters.registerSetter('DJStringSetter', DJStringSetter);
},
};
}
customSetter.pluginName = 'customSetter';
await plugins.register(customSetter);

```

低代码生态贡献

可以使用脚手架提供插件或者物料以及设置器（例如：[dj-material-test](https://github.com/Hdj2048228/dj-material-test)）

```
TypeScript
npm init @alilc/element your-element-name
--registry=https://registry.npmmirror.com
cd your-element-name
npm install
npm start

```

**3. 编排、渲染**

渲染依赖于 schema 和 components。其中 schema 和 components
需要一一对应，schema 中使用到的组件都需要在 components
中进行声明，否则无法正常渲染。[详情见官网](https://lowcode-engine.cn/docV2/nhilce)

```
JavaScript
import ReactRenderer from '@alilc/lowcode-react-renderer';
import ReactDOM from 'react-dom';
import { Button } from '@alifd/next';

const schema = {
componentName: 'Page',
props: {},
children: [
{
componentName: 'Button',
props: {
type: 'primary',
style: {
color: '#2077ff'
},
},
children: '确定',
},
],
};

const components = {
Button,
};

ReactDOM.render((
<ReactRendererschema={schema}components={components}
/>
), document.getElementById('root'));

```

**4. 出码**

所谓出码，即将低代码编排出的 schema
进行解析并转换成最终可执行的代码的过程。出码是为了更高效的运行和更灵活地定制渲染，基于
Schema
的运行时渲染，有着能实时响应内容的变化和接入成本低的优点，但是也存在着实时解析运行的性能开销比较大和包大小比较大的问题，所以并不是所有场景都需要出码。

场景一：想要极致的打开速度，降低
LCP/FID,使用出码以减少运行时的schema解析，将其放入到编译时，前端依赖包大小就会减少许多。从而也提升了加载速度。

场景二：老项目 +
新需求，想用搭建产出，只在低代码平台上搭建新的业务页面，然后通过出码模块导出这些页面的源码，连同一些全局依赖模块，一起
Merge 到老项目中。

场景三：协议不能描述的复杂代码逻辑（协议功能不足或特别定制化的逻辑）

   ```
   // 安装依赖:
   npm install --save @alilc/lowcode-code-generator
   
   import { plugins } from '@alilc/lowcode-engine';
   import CodeGenPlugin from '@alilc/lowcode-plugin-code-generator';
   
   // 在你的初始化函数中：await plugins.register(CodeGenPlugin);
   
   // 如果您不希望自动加上出码按钮，则可以这样注册await
   plugins.register(CodeGenPlugin, { disableCodeGenActionBtn: true });
   //引入代码生成器:
   import * as CodeGenerator from
   '@alilc/lowcode-code-generator/standalone-loader';
   
   // 出码
   const result = await CodeGenerator.generateCode({
   solution: 'icejs', // 出码方案 (目前内置有 icejs 和 rax )
   schema, // 编排搭建出来的 schema
   });
   
   console.log(result); // 出码结果(默认是递归结构描述的，可以传
   flattenResult: true 以生成扁平结构的结果)
   
   // **自定义出码**
   npx @alilc/lowcode-code-generator init-solution <your-solution-name>
   
   ```
**5. 对于vue的支持**

lowcode-engine本身是和框架无关的，如果需要相应的框架可以根据官方指引进行开发，对于vue来说低代码引擎官方短期内并没有支持的计划，但有社区有研发相关的Vue
画布--[肯耐珂萨团队实现的 Vue 画布](https://github.com/KNXCloud/lowcode-engine-vue)

**四、对于租户端低代码模块的启发**

租户端低代码模块是基于数据模型驱动的，可以创建自定义对象以无代码的形式创建对象字段的新增、列表、详情等页面，低代码模块对于物料组件亦定义了相应的协议规范，其编排模块和运行时也有其相应的机制渲染机制，只是缺乏针对客户的定制开发的能力，后续所做的是要打好基础，统一项目中的物料组件规范，形成标准化XsUI，提供相应的接口和API支持客户定制开发自定义业务组件，对于系统中的对象获取、组织角色权限等功能封装相应的xs-sdk提供客户统一使用，满足客户的复杂业务的开发，降低开发的成本提升效率。

**五、总结**

现在市面上低代码平台众多纷纭，每个低代码平台的实现方式和适用场景大不相同，低代码引擎提供了统一的协议标准，对于整个低代码平台的后续发展有着重要意义。通过统一的标准接口实现，可以说lowcode-engine是低代码平台的基础架构，通过低代码引擎丰富的生态能力能够孵化出适用于更多的业务场景的低代码平台。

对于开发者自身而言，低代码引擎只是一个用来减少重复劳动做的工具，是用来解决通用且可复用的产品能力，能够将我们从枯燥重复的接口开发中解放出来，让我们投入到更有意义、价值的研究之中，同时低代码并不能实现所有业务场景，所以在实现低代码平台时依然要编写部分代码实现部分需求，这就要求我们在开发低代码平台时留有相应的机制能够定制化开发完成复杂的业务场景。

**参考**

[低代码引擎文章专区](https://www.yuque.com/lce/xhk5hf)

[低代码引擎官网](https://lowcode-engine.cn/)

[实战参考-blibli](https://www.bilibili.com/video/BV1B44y1P7GM/?spm_id_from=333.788&vd_source=578006bc07956b0802cfe6b0dfcad2cb)

[《基于 Lerna 管理 packages 的 Monorepo
项目最佳实践》](https://juejin.cn/post/6844903911095025678)

[阿里低代码引擎和生态建设实战及思考](https://mp.weixin.qq.com/s?__biz=Mzg4MjE5OTI4Mw==&mid=2247489735&idx=1&sn=c3841b1731fde2999ea6a617c91b9ed2&scene=21#wechat_redirect)

[阿里低代码引擎 LowCodeEngine
正式开源！](https://mp.weixin.qq.com/s/T66LghtWLz2Oh048XqaniA)



