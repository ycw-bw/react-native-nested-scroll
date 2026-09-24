> 文档模板：v0.4.2

<p align="center">
  <h1 align="center"> <code>react-native-nested-scroll</code> </h1>
</p>

本项目基于 [react-native-nested-scroll@0.14.0](https://github.com/sdcxtech/react-native-troika/tree/master/packages/nested-scroll) 开发。

该第三方库支持从 npm 下载，新的包名为：`@react-native-ohos/react-native-nested-scroll`，版本所属关系如下：

| 三方库名称 | 三方库版本（npm地址） | 发布信息 | 支持RN版本 | Autolink | 编译API版本 | 社区基线版本 | 源码地址 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| @react-native-ohos/react-native-nested-scroll | [~0.15.0](https://www.npmjs.com/package/@react-native-ohos/react-native-nested-scroll) | [Github Releases](https://github.com/react-native-oh-library/react-native-nested-scroll/releases) | 0.82.* | 是 | API12+ | 0.14.0 | [br_rnoh0.82](https://github.com/react-native-oh-library/react-native-nested-scroll/tree/br_rnoh0.82) |

## 简介

NestedScrollView 是一个 React Native 原生 UI 组件，用于实现如下结构的视图：最外层是一个可纵向滚动的视图，即 NestedScrollView，它的子组件往往由一个头部（NestedScrollViewHeader）和一个可横向滚动的视图组成；最里层是若干也可以纵向滚动的视图，如 ScrollView、FlashList、WebView 等。<br/>
NestedScrollView 的作用是协调最里层和最外层可滚动视图之间的（纵向）滚动，使得滚动体验更加流畅。

## 下载安装

进入到工程目录并输入以下命令：

**npm**

```bash
npm install @react-native-ohos/react-native-nested-scroll
```

**yarn**

```bash
yarn add @react-native-ohos/react-native-nested-scroll
```

## Link

| 版本 | 是否支持autolink | RN框架版本 |
|------|----------------|-----------|
| ~0.15.0 | 是 | 0.82.* |

使用AutoLink的工程需要根据该文档配置，Autolink框架指导文档：https://gitcode.com/CPF-RN/ohos_react_native/blob/main/docs/zh-cn/02-开发/02-开发指南/Autolinking.md

如您使用的版本支持 Autolink，并且工程已接入 Autolink，可跳过ManualLink配置。
<details>
  <summary>ManualLink: 此步骤为手动配置原生依赖项的指导</summary>

首先需要使用 DevEco Studio 打开项目里的 HarmonyOS 工程 `harmony`。

### 1. Overrides RN SDK

为了让工程依赖同一个版本的 RN SDK，需要在工程根目录的 `oh-package.json5` 添加 overrides 字段，指向工程需要使用的 RN SDK 版本。替换的版本既可以是一个具体的版本号，也可以是一个模糊版本，还可以是本地存在的 HAR 包或源码目录。

关于该字段的作用请阅读[官方说明](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/ide-oh-package-json5-V5#zh-cn_topic_0000001792256137_overrides)

```json
{
  "overrides": {
    "@rnoh/react-native-openharmony": "~0.82.18" // ohpm 在线版本
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony.har" // 指向本地 har 包的路径
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony" // 指向源码路径
  }
}
```

### 2. 引入原生端代码

目前有两种方法：

- 通过 har 包引入；
- 直接链接源码。

方法一：通过 har 包引入（推荐）

> [!TIP] har 包位于三方库安装路径的 `harmony` 文件夹下。

打开 `entry/oh-package.json5`，添加以下依赖

```json
"dependencies": {
    "@react-native-ohos/react-native-nested-scroll": "file:../../node_modules/@react-native-ohos/react-native-nested-scroll/harmony/nested_scroll.har"
  }
```

点击右上角的 `sync` 按钮

或者在命令行终端执行：

```bash
cd entry
ohpm install
```

方法二：直接链接源码

> [!TIP] 如需使用直接链接源码，请参考[直接链接源码说明](https://gitcode.com/CPF-RN/usage-docs/blob/master/zh-cn/link-source-code.md)

### 3. 配置 CMakeLists 和引入 NestedScrollViewPackage

打开 `entry/src/main/cpp/CMakeLists.txt`，添加：

```diff
project(rnapp)
cmake_minimum_required(VERSION 3.4.1)
set(CMAKE_SKIP_BUILD_RPATH TRUE)
set(RNOH_APP_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
set(NODE_MODULES "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../node_modules")
set(OH_MODULE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../oh_modules")
set(RNOH_CPP_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../oh_modules/@rnoh/react-native-openharmony/src/main/cpp")
set(RNOH_GENERATED_DIR "${CMAKE_CURRENT_SOURCE_DIR}/generated")
set(LOG_VERBOSITY_LEVEL 1)
set(CMAKE_ASM_FLAGS "-Wno-error=unused-command-line-argument -Qunused-arguments")
set(CMAKE_CXX_FLAGS "-fstack-protector-strong -Wl,-z,relro,-z,now,-z,noexecstack -s -fPIE -pie")
+ set(OH_MODULES "${CMAKE_CURRENT_SOURCE_DIR}/../../../oh_modules")

set(WITH_HITRACE_SYSTRACE 1) # for other CMakeLists.txt files to use
add_compile_definitions(WITH_HITRACE_SYSTRACE)

add_subdirectory("${RNOH_CPP_DIR}" ./rn)

# RNOH_BEGIN: manual_package_linking_1
+ add_subdirectory("${OH_MODULES}/@react-native-ohos/react-native-nested-scroll/src/main/cpp" ./nested-scroll)
# RNOH_END: manual_package_linking_1

file(GLOB GENERATED_CPP_FILES "${RNOH_CURRENT_SOURCE_DIR}/generated/*.cpp")

add_library(rnoh_app SHARED
    ${GENERATED_CPP_FILES}
    "./PackageProvider.cpp"
    "${RNOH_CPP_DIR}/RNOHAppNapiBridge.cpp"
)

target_link_libraries(rnoh_app PUBLIC rnoh)

# RNOH_BEGIN: manual_package_linking_2
+ target_link_libraries(rnoh_app PUBLIC rnoh_nested_scroll)
# RNOH_END: manual_package_linking_2
```

打开 `entry/src/main/cpp/PackageProvider.cpp`，添加：

```diff
#include "RNOH/PackageProvider.h"
+ #include "NestedScrollViewPackage.h"

using namespace rnoh;

std::vector<std::shared_ptr<Package>> PackageProvider::getPackages(Package::Context ctx) {
    return {
+     std::make_shared<NestedScrollViewPackage>(ctx),
    };
}
```
</details>

### 运行

点击右上角的 `sync` 按钮

或者在命令行终端执行：

```bash
cd entry
ohpm install
```

然后编译、运行即可。

## 约束与限制

### 兼容性

本文档内容基于以下版本验证通过：

1. RNOH: 0.82.1; SDK: HarmonyOS 6.0.0 Release SDK; IDE: DevEco Studio 6.0.0.858; ROM: 6.0.0.112;

### 权限要求

无特殊权限要求。

## 使用示例

下面的代码展示了这个库的基本使用场景：

> [!WARNING] 使用时 import 的库名不变。

```jsx
import React from 'react';
import {ScrollView, StyleSheet, Text, View} from 'react-native';
import {NestedScrollView, NestedScrollViewHeader} from '@react-native-ohos/react-native-nested-scroll';

function NestedScrollFlatList() {
  return (
    <NestedScrollView style={styles.coordinator} bounces = {false}>
      <NestedScrollViewHeader stickyHeight={60}>
        <Text style={styles.text}>anchorTest</Text>
        <Text style={styles.text}>anchor</Text>
      </NestedScrollViewHeader>

      <View style={{ height: "90%"}}>

        <ScrollView>
          <Text style={{ height: 30}}>test1</Text>
          <Text style={{ height: 30}}>test2</Text>
          <Text style={{ height: 30}}>test3</Text>
          <Text style={{ height: 30}}>test4</Text>
          <Text style={{ height: 30}}>test5</Text>
          <Text style={{ height: 30}}>test6</Text>
          <Text style={{ height: 30}}>test7</Text>
          <Text style={{ height: 30}}>test8</Text>
          <Text style={{ height: 30}}>test9</Text>
          <Text style={{ height: 30}}>test10</Text>
          <Text style={{ height: 30}}>test11</Text>
          <Text style={{ height: 30}}>test12</Text>
          <Text style={{ height: 30}}>test13</Text>
          <Text style={{ height: 30}}>test14</Text>
          <Text style={{ height: 30}}>test15</Text>
          <Text style={{ height: 30}}>test16</Text>
          <Text style={{ height: 30}}>test17</Text>
          <Text style={{ height: 30}}>test18</Text>
          <Text style={{ height: 30}}>test19</Text>
          <Text style={{ height: 30}}>test20</Text>
          <Text style={{ height: 30}}>test21</Text>
          <Text style={{ height: 30}}>test22</Text>
          <Text style={{ height: 30}}>test23</Text>
          <Text style={{ height: 30}}>test24</Text>
          <Text style={{ height: 30}}>test25</Text>
          <Text style={{ height: 30}}>test26</Text>
          <Text style={{ height: 30}}>test27</Text>
          <Text style={{ height: 30}}>test28</Text>
          <Text style={{ height: 30}}>test29</Text>
          <Text style={{ height: 30}}>test30</Text>
        </ScrollView>
      </View>
    </NestedScrollView>
  );
}

const styles = StyleSheet.create({
  coordinator: {
    backgroundColor: '#fff',
  },
  content: {
    backgroundColor: '#0000FF',
    justifyContent: 'center',
    alignItems: 'center',
  },
  image: {
    height: 160,
    width: '100%',
  },
  text: {
    lineHeight: 60,
    fontSize: 18,
    color: '#0xCCCCCC',
  },
});
export default NestedScrollFlatList
```

## 使用说明

**基本使用**

```jsx
<NestedScrollView style={styles.coordinator} bounces = {false}>
   <NestedScrollViewHeader stickyHeight={60}>
        <Image source={require('../assets/cover.webp')} style={styles.image} resizeMode="cover" />
        <Text style={styles.text}>anchor</Text>
    </NestedScrollViewHeader>
</NestedScrollView>
```

**注意事项**

在 Android 上，本库基于 NestedScrolling API 实现，请记得为最内层可滚动视图开启 `nestedScrollEnabled` 属性。

## 接口说明

> [!TIP] "Platform"列表示该属性在原三方库上支持的平台。

> [!TIP] "OpenHarmony Support"列为 yes 表示 OpenHarmony平台支持 该属性；no 则表示不支持；partially 表示部分支持。使用方法跨平台一致，效果对标 iOS 或 Android 的效果。

### 组件

| 名称       | 参数类型     | 必填  | 平台  | OpenHarmony平台支持 | 描述 |
|------------|------------|------|------|-----|----------------|
| NestedScrollView | [NestScrollProps](#NestScrollProps) | no | all | Yes | 嵌套滚动视图容器，协调最里层与最外层可滚动视图之间的纵向滚动。 |
| NestedScrollViewHeader | [NestedScrollViewHeaderProps](#NestedScrollViewHeaderProps) | no | all | Yes | 嵌套滚动的头部视图，支持将子组件固定在顶部。 |

### 属性

NestScrollProps

| 名称 | 参数类型 | 默认值 | 必填 | 平台 | OpenHarmony平台支持 | 描述 |
| --- | --- | --- | --- | --- | --- | --- |
| bounces | boolean | false | no | iOS | Yes | 设置 NestedScrollView 是否有弹性。一旦设置为 true，最内层可滚动视图将失去弹性。 |

NestedScrollViewHeaderProps

| 名称 | 参数类型 | 默认值 | 必填 | 平台 | OpenHarmony平台支持 | 描述 |
| --- | --- | --- | --- | --- | --- | --- |
| stickyHeight | number | -1 | no | Android、iOS | Yes | 表示 header 多高的区域将会被固定在顶部。 |
| stickyHeaderBeginIndex | number | -1 | no | Android、iOS | Yes | 表示从第几个子组件开始，子组件将会被固定在顶部。 |
| onScroll | function | None | no | Android、iOS | Yes | 滚动回调函数，可用于实现头部视图的视差效果。 |

## 遗留问题

无

## 其他
无

## 目录结构
````
/react-native-nested-scroll  # 项目根目录
├── harmony                       # 鸿蒙适配代码
│   ├── nested_scroll.har         # har包
│   └── nested_scroll             # 鸿蒙适配核心代码
│       ├── Index.ets             # 鸿蒙适配代码入口
│       ├── ts.ts                 # TypeScript 导出入口
│       └── src/main
│           ├── cpp               # C++ 适配代码（NestedScrollViewPackage、ComponentInstance、Props、ShadowNodes 等）
│           ├── module.json5      # 模块配置文件
│           └── resources         # 资源文件
├── src                           # RN代码
│   ├── index.tsx                 # 入口文件，导出 NestedScrollView 与 NestedScrollViewHeader
│   ├── nestedScrollNativeComponent.ts  # NestedScrollView 原生组件 Codegen 声明
│   └── NestedScrollViewHeader    # NestedScrollViewHeader 组件
│       ├── index.tsx             # NestedScrollViewHeader 组件封装
│       └── nestedScrollHeaderNativeComponent.ts  # Header 原生组件 Codegen 声明
├── react-native.config.js        # RN 配置文件
├── package.json                  # 包配置文件
├── CHANGELOG.md                  # 版本变更记录
├── README.OpenSource             # 开源信息声明
├── LICENSE                       # 开源协议文件
├── README.md                     # 中文安装使用方法
└── README_en.md                  # 英文安装使用方法
````

## 贡献代码

使用过程中发现任何问题都可以提交 [Issue](https://github.com/react-native-oh-library/react-native-nested-scroll/issues)，当然，也非常欢迎提交 [PR](https://github.com/react-native-oh-library/react-native-nested-scroll/pulls) 。

## 开源协议

本项目基于 [The MIT License (MIT)](https://github.com/sdcxtech/react-native-troika/blob/master/packages/nested-scroll/LICENSE) ，请自由地享受和参与开源。
