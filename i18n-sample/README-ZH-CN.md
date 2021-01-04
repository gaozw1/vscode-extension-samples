# I18s示例

[en](./READMD.md)/zh

这个文件夹包含了一个VS code扩展，作用是演示如何使用`package.nls.json`和`vcode -nls`库将项目本地化。对于这个示例，它用英语和日语显示了两个命令:Hello和Bye。

假设

- 所有的本地化文件都在i18n文件夹下。
- 您可以手动创建这个文件夹，也可以使用`vcode -nls-dev`工具解压缩它。
- 在i18n文件夹下，有你想要本地化的语言的子文件夹。这些名称遵循ISO 639-3约定。
- 在language names文件夹下，您将创建json文件，并为你的扩展的映射出源代码结构(例如，out/src)。json文件是要本地化文本的键/值对。命名约定为`<file_name>.i18n.json`。
- 如果在你的扩展中有一个顶级`package.nls.json`，遵循`package.i18n.json`的命名约定，每种语言都应该有一个`package.nls.json`。

## 演示

![Demo](https://raw.githubusercontent.com/gaozw1/vscode-extension-samples/master/i18n-sample/demo.gif)

## 运行示例

本地化值只在运行gulp`build`任务时应用。在使用`tsc -watch`编译的正常开发过程中，不会发生本地化后处理。这加快了开发时间。

1. 确保你已经使用`npm install --global gulp-cli`全局安装了`gulp-cli`。
2. 运行`npm install`来引入依赖项。
3. 按照[https://code.visualstudio.com/api/working-with-extensions/publishing-extension](https://code.visualstudio.com/api/working-with-extensions/publishing-extension)上的步骤操作，以确保您已经安装了vsce并拥有一个发行者帐户。
4. 运行`gulp package`生成一个.vsix文件。
5. 按照[https://code.visualstudio.com/docs/editor/extension-gallery#_install-from-a-vsix](https://code.visualstudio.com/docs/editor/extension-gallery#_install-from-a-vsix)上的说明安装.vsix文件
6. 通过从命令面板调用“Configure Language”，将本地设置更改为日语。

请查看这个存储库中的demo.gif文件以获取一个截屏。

## 如何翻译我们的扩展

VS Code本身使用[Transifex](https://www.transifex.com/)来管理它的翻译。这可能也是你扩展的一个选择，但是`vscode-nls`或`vscode-nls-dev`提供的nls工具都不需要Transifex作为它的翻译平台。所以你可以自由选择一个不同的。

## 在后台发生了什么

1. `vcode -nls-dev`模块用于重写生成的JavaScript。
2. `localalize ('some_key'， 'Hello')`的调用被转换为`localalize (0, null)`，其中第一个参数(在本例中为0)是密钥在消息文件中的位置。
3. i18n文件夹的内容将从键/值对转换为位置数组。

## 注意事项

你可以使用自己的本地化管道。

1. 本地化在你的`package.json`可以通过将本地化后的文本包装成`%some.key%`的形式来实现。

```json
// [Before] package.json

  "contributes": {
    "commands": [
      {
        "command": "extension.sayHello",
        "title": "Hello"
      }
    ]

// [After] package.json

  "contributes": {
    "commands": [
      {
        "command": "extension.sayHello",
        "title": "%extension.sayHello.title%"
      }
    ]

// [After] new package.nls.json

{
    "extension.sayHello.title": "Hello",
}


```


然后，创建相应的`package.nls.{your_language}.json`文件用于本地化每种语言。

2. 也可以使用自己的库来本地化源文件中的文本。您可以使用`process.env.VSCODE_NLS_CONFIG`环境变量。在运行时，这个环境变量是一个JSON字符串，包含了VS Code运行时使用的区域设置。例如，这是日语的值:`"{"locale":"ja","availableLanguages":{"*":"ja"}}"`

```js
function localize(config) {
  const messages = {
    en: 'Hello',
    ja: 'こんにちは'
  };
  return messages[config['locale']];
}

const config = JSON.parse(process.env.VSCODE_NLS_CONFIG);
localize(config);
```
