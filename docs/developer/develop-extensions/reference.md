## index.js

index.js文件的内容描述了此插件的名称，版本，作者等显示内容，也描述了此插件的积木渲染，代码转译等函数文件的位置。

- 参数说明（*以下带有星号\*的参数为必须参数*）：

    - **name***

        显示在扩展选择界面的名字

    - **extensionId***

        扩展的唯一ID

    - **version**

        显示在扩展选择界面的版本号

    - **supportDevice***

        支持的设备，扩展只会选择了对应支持的设备后才会在扩展选择界面显示

    - **author**

        显示在扩展选择界面的作者名字

    - **iconURL***

        显示在扩展选择界面的图片。使用像素600x372，并在周围留有一定白边的图片以达到最好的显示效果。

    - **description**

        显示在扩展选择界面图片下方扩展介绍内容

    - **featured***

        此参数为内部参数，必须设置为true

    - **blocks***

        扩展的积木渲染生成代码文件

    - **generator***

        扩展的积木代码转译文件

    - **toolbox***

        扩展的工具区代码文件

    - **msg***

        扩展的本地化翻译文件

    - **library**

        存放插件需要需要使用的代码库

    - **tags***

        标识此插件的类目。可选选项有：actuator, sensor, display, communication, other, kit(此类为套件类产品插件的专属标签)。

    - **helpLink**

        帮助链接，点击插件页中的帮助将打开此链接。建议设置为插件的在线Wiki地址。

- 本地化支持

    此文件中 name，description 等属性均可以使用 formatMessage 函数实现多语言支持。

    ```js
    {
        name: formatMessage({
        id: 'xxx.description',
        default: 'xxx',
        description: 'xxx'
        }),
        description: formatMessage({
            id: 'xxx.description',
            default: 'xxx',
            description: 'xxx'
        }),
    }
    ```
    
    - id
        此文本的唯一识别id

    - default
        缺失翻译时的默认显示内容

    - description
        供翻译者参考的文本内容说明

    而后在 extensions/locales.js 文件中加入新增的 formatMessage 的 id 与本地化翻译。

### blocks.js

此文件定义描述了积木的外观和行为，包括文本，颜色，形状以及它可以连接的其他块。

此部分内容可以直接参考 google blockly 项目的说明：[https://developers.google.com/blockly/guides/create-custom-blocks/overview](https://developers.google.com/blockly/guides/create-custom-blocks/overview)

### toolbox.js

此文件定义了积木在左侧工具区中的显示内容以及输入框中的默认值。

```xml
function addToolbox () {
    return `
<category name="%{BKY_XXX_CATEGORY}" id="XXX_CATEGORY" colour="#42CCFF" secondaryColour="#42CCFF">
    <block type="xxx_init" id="xxx_init">
        <value name="no">
            <shadow type="math_number">
                <field name="NUM">1</field>
            </shadow>
        </value>
        <value name="pin">
            <shadow type="math_number">
                <field name="NUM">2</field>
            </shadow>
        </value>
    </block>
</category>`;
}

exports = addToolbox;
```

此处目录的名称为了多语言翻译，调用了 msg 中 XXX_CATEGORY 的值，不过在这里使用时需要在前方添加`BKY_`

此部分内容可以直接参考 google blockly 项目的说明：[https://developers.google.com/blockly/guides/configure/web/toolbox](https://developers.google.com/blockly/guides/configure/web/toolbox)

### generator.js

此文件中定义了积木的代码转译内容。

#### 获取积木参数

在转译函数中，有两个函数方式来获取积木内部参数：

- **Blockly.Arduino.valueToCode**：

此函数用于获取积木内部的可以接收其他积木输入的参数内容（由于这类输入框中可能会放置其他积木，使用该函数会继续调用解析其内容积木内容）。函数第一个参数输入这个积木本身即this，第二个参数为此输入框的名称，第三个参数为参优先级，直接默认使用Blockly.Arduino.ORDER_ATOMIC即可。

- **this.getFieldValue**

此函数用于获取积木内部下拉框参数的内容

#### 生成代码

积木生成的代码分为两个部分，在积木本身位置生成的代码需要在return中返回，如果积木是一个输出类型，还需要在返回时返回一个代码优先级，此处直接使用 ORDER_ATOMIC 即最高优先级即可。

如果积木生成的代码还需要再其他位置添加如变量定义，库的调用等代码，则为以下变量数字赋值，会在代码区对应的位置生成定义的内容。注意这里为数组形式，相同名称的数组内容不会重复生成。

| 生成器   | 可用变量                                                                                                                                     |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| arduino | Blockly.Arduino.includes\_, Blockly.Arduino.definitions\_, Blockly.Arduino.definitions\_, Blockly.Arduino.setups\_, Blockly.Arduino.loops\_ |
| python  | Blockly.Python.imports\_, Blockly.Python.libraries\_, Blockly.Python.setups\_, Blockly.Python.loops\_                                       |

### msg.js

用来实现多语言支持功能，定义了积木以及工具区目录的显示内容。

```js
function addMsg (Blockly) {
    Object.assign(Blockly.ScratchMsgs.locales.en, {
        XXX_CATEGORY: 'XXX',
        XXX_INIT: 'init xxx %1 pin %2 mode %3',
    });
    Object.assign(Blockly.ScratchMsgs.locales['zh-cn'], {
        XXX_CATEGORY: 'XXX',
        XXX_INIT: '初始化 xxx %1 引脚 %2 型号 %3',
    });
    return Blockly;
}

exports = addMsg;

```

!!! Info
    仔细翻看 openblock 中现有的插件代码你会发现很多新奇的函数实现及上方未介绍的内容，这是因为 openblock-blocks 软件包内容衍生于 Google 的 blockly 开源项目，其中的函数实现有很多方式，实难详尽介绍。想要详细了解更多关于 blocks 定义函数的内容，你可以访问 [blockly 项目 Wiki](https://developers.google.com/blockly) 来了解开发更多的用法和积木模块。注意 openblock-blocks 分支于 blockly 早期版本，且做了一定程度的定制化修改，所以并非所有 blockly 文档均适用于 Openblock。
