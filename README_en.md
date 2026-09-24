> Document Template: v0.4.2

<p align="center">
  <h1 align="center"> <code>react-native-nested-scroll</code> </h1>
</p>

This project is based on [react-native-nested-scroll@0.14.0](https://github.com/sdcxtech/react-native-troika/tree/master/packages/nested-scroll).

This third-party library supports direct download from npm, the new package name is: `@react-native-ohos/react-native-nested-scroll`, the version correspondence details are as follows:

| Name | Version(Npm Address) | Release Information | Supported RN Version | Supported Autolink | Compile API Version | Community Baseline Version | Source code address |
| --- | --- | --- | --- | --- | --- | --- | --- |
| @react-native-ohos/react-native-nested-scroll | [~0.15.0](https://www.npmjs.com/package/@react-native-ohos/react-native-nested-scroll) | [GitCode Releases](https://gitcode.com/CPF-RN/react-native-nested-scroll/releases) | 0.82.* | Yes | API12+ | 0.14.2 | [br_rnoh0.82](https://github.com/react-native-oh-library/react-native-nested-scroll/tree/br_rnoh0.82) |
| @react-native-ohos/react-native-nested-scroll | [~0.14.3](https://www.npmjs.com/package/@react-native-ohos/react-native-nested-scroll) | [GitCode Releases](https://gitcode.com/CPF-RN/react-native-nested-scroll/releases) | 0.77.* | No | API12+ | 0.14.2 | [sig](https://github.com/react-native-oh-library/react-native-nested-scroll/tree/sig) |
| @react-native-ohos/react-native-nested-scroll | [~0.14.3](https://www.npmjs.com/package/@react-native-ohos/react-native-nested-scroll) | [Github Releases](https://github.com/react-native-oh-library/react-native-nested-scroll/releases) | 0.72.* | No | API12+ | 0.14.0 | [sig](https://github.com/react-native-oh-library/react-native-nested-scroll/tree/sig) |
| @react-native-oh-tpl/react-native-nested-scroll | [<=0.14.2@deprecated](https://www.npmjs.com/package/@react-native-oh-tpl/react-native-nested-scroll) | [Github Releases(deprecated)](https://github.com/react-native-oh-library/react-native-nested-scroll/releases) | 0.72.* | No | API12+ | 0.14.2 | [sig](https://github.com/react-native-oh-library/react-native-nested-scroll) |

## Introduction

NestedScrollView is a native UI component for React Native. It is used to implement view structures where the outermost layer is a vertically scrollable view, i.e. NestedScrollView, whose children usually consist of a header (NestedScrollViewHeader) and a horizontally scrollable view, and the innermost layer consists of several vertically scrollable views, such as ScrollView, FlashList, WebView, etc.<br/>
NestedScrollView coordinates the vertical scrolling between the innermost and outermost scrollable views, making the scrolling experience smoother.

## Installation

Go to the project directory and execute the following instruction:

**npm**

```bash
npm install @react-native-ohos/react-native-nested-scroll
```

**yarn**

```bash
yarn add @react-native-ohos/react-native-nested-scroll
```

## Link

| Version | Supported Autolink | Supported RN Version |
|------|--------------------|----------------------|
| ~0.14.3 | No | 0.72.* |
| <=0.14.2@deprecated | No | 0.72.* |

Projects using AutoLink need to be configured according to this document, AutoLink framework guide: https://gitcode.com/CPF-RN/ohos_react_native/blob/main/docs/en/02-development/02-development-guide/autolinking.md

ManualLink: This step provides guidance for manually configuring native dependencies.

Open the `harmony` directory of the OpenHarmony project in DevEco Studio.

### 1. Overrides RN SDK

To ensure the project relies on the same version of the RN SDK, you need to add an `overrides` field in the project's root `oh-package.json5` file, specifying the RN SDK version to be used. The replacement version can be a specific version number, a semver range, or a locally available HAR package or source directory.

For more information about the purpose of this field, please refer to the [official documentation](https://developer.huawei.com/consumer/en/doc/harmonyos-guides-V5/ide-oh-package-json5-V5#en-us_topic_0000001792256137_overrides).

```json
{
  "overrides": {
    "@rnoh/react-native-openharmony": "~0.72.38" // ohpm version
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony.har" // a locally available HAR package
    // "@rnoh/react-native-openharmony" : "./react_native_openharmony" // source code directory
  }
}
```

### 2. Introducing Native Code

Currently, two methods are available:

- Use the HAR file.
- Directly link to the source code.

Method 1 (recommended): Use the HAR file.

> [!TIP] The HAR file is stored in the `harmony` directory in the installation path of the third-party library.

Open `entry/oh-package.json5` file and add the following dependencies:

```json
"dependencies": {
    "@react-native-ohos/react-native-nested-scroll": "file:../../node_modules/@react-native-ohos/react-native-nested-scroll/harmony/nested_scroll.har"
  }
```

Click the `sync` button in the upper right corner.

Alternatively, run the following instruction on the terminal:

```bash
cd entry
ohpm install
```

Method 2: Directly link to the source code.

> [!TIP] For details, see [Directly Linking Source Code](https://gitcode.com/CPF-RN/usage-docs/blob/master/en/link-source-code.md).

### 3. Configuring CMakeLists and Introducing NestedScrollViewPackage

Open `entry/src/main/cpp/CMakeLists.txt` and add the following code:

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

Open `entry/src/main/cpp/PackageProvider.cpp` and add the following code:

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


### Running

Click the `sync` button in the upper right corner.

Alternatively, run the following instruction on the terminal:

```bash
cd entry
ohpm install
```

Then build and run the code.

## Constraints

### Compatibility

This document is verified based on the following versions:

1. RNOH: 0.72.96; SDK: HarmonyOS 6.0.0 Release SDK; IDE: DevEco Studio 6.0.0.858; ROM: 6.0.0.112;

### Permission Requirements

No special permission is required.

## Example

The following code shows the basic use scenario of the repository:

> [!WARNING] The name of the imported repository remains unchanged.

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

## How to Use

**Basic Usage**

```jsx
<NestedScrollView style={styles.coordinator} bounces = {false}>
   <NestedScrollViewHeader stickyHeight={60}>
        <Image source={require('../assets/cover.webp')} style={styles.image} resizeMode="cover" />
        <Text style={styles.text}>anchor</Text>
    </NestedScrollViewHeader>
</NestedScrollView>
```

**Note**

On Android, this library is implemented based on the NestedScrolling API. Remember to enable the `nestedScrollEnabled` property for the innermost scrollable views.

## Available APIs

> [!TIP] The **Platform** column indicates the platform where the properties are supported in the original third-party library.

> [!TIP] If the value of **HarmonyOS Support** is **yes**, it means that the OpenHarmony platform supports this property; **no** means the opposite; **partially** means some capabilities of this property are supported. The usage method is the same on different platforms and the effect is the same as that of iOS or Android.

### Components

| Name         | Parameter Type                 | Required  | Platform  | OpenHarmony Platform Support | Description          |
|--------------|------------------------|------|------|--------------|----------------------|
| NestedScrollView | [NestScrollProps](#NestScrollProps) | no | all | Yes | A nested scroll view container that coordinates vertical scrolling between the innermost and outermost scrollable views. |
| NestedScrollViewHeader | [NestedScrollViewHeaderProps](#NestedScrollViewHeaderProps) | no | all | Yes | The header view of nested scrolling, which supports pinning child components to the top. |

### Properties

NestScrollProps

| Name | Parameter Type | Default Value | Required | Platform | OpenHarmony Platform Support | Description |
| --- | --- | --- | --- | --- | --- | --- |
| bounces | boolean | false | no | iOS | Yes | Sets whether NestedScrollView is bouncy. Once set to true, the innermost scrollable view loses its bounce effect. |

NestedScrollViewHeaderProps

| Name | Parameter Type | Default Value | Required | Platform | OpenHarmony Platform Support | Description |
| --- | --- | --- | --- | --- | --- | --- |
| stickyHeight | number | -1 | no | Android, iOS | Yes | Indicates how tall a region of the header will be pinned to the top. |
| stickyHeaderBeginIndex | number | -1 | no | Android, iOS | Yes | Indicates the index of the child component from which child components will be pinned to the top. |
| onScroll | function | None | no | Android, iOS | Yes | Scroll callback function, which can be used to implement a parallax effect for the header view. |

## Known Issues

None

## Other

None

## Directory Structure
````
/react-native-nested-scroll  # Project root directory
├── harmony                       # HarmonyOS adaptation code
│   ├── nested_scroll.har         # har package
│   └── nested_scroll             # Core HarmonyOS adaptation code
│       ├── Index.ets             # Entry file of the HarmonyOS adaptation code
│       ├── ts.ts                 # TypeScript export entry
│       └── src/main
│           ├── cpp               # C++ adaptation code (NestedScrollViewPackage, ComponentInstance, Props, ShadowNodes, etc.)
│           ├── module.json5      # Module configuration file
│           └── resources         # Resource files
├── src                           # React Native code
│   ├── index.tsx                 # Entry file, exporting NestedScrollView and NestedScrollViewHeader
│   ├── nestedScrollNativeComponent.tsx # Native component Codegen declaration of NestedScrollView
│   └── NestedScrollViewHeader    # NestedScrollViewHeader component
│       ├── index.tsx             # NestedScrollViewHeader component wrapper
│       └── nestedScrollHeaderNativeComponent.tsx # Native component Codegen declaration of the Header
├── react-native.config.js        # RN configuration file
├── package.json                  # Package configuration file
├── CHANGELOG.md                  # Version change records
├── README.OpenSource             # Open-source information statement
├── LICENSE                       # License file
├── README.md                     # Chinese installation and usage instructions
└── README_en.md                  # English installation and usage instructions
````

## How to Contribute

If you find any problem during the use, you can submit an [Issue](https://github.com/react-native-oh-library/react-native-nested-scroll/issues). Of course, we also welcome you to submit a [PR](https://github.com/react-native-oh-library/react-native-nested-scroll/pulls).

## License

This project is based on [The MIT License (MIT)](https://github.com/sdcxtech/react-native-troika/blob/master/packages/nested-scroll/LICENSE). Feel free to enjoy and participate in open source.