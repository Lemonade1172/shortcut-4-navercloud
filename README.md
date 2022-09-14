# shortcut-4-navercloud

Interview questions

试题如下：
现需要设计一个聊天常用语热键，主要覆盖如下功能

1.支持热键的创建。要求：热键的创建为功能键+数字键，如 ctrl+ [0-9]

2.支持热键的使用。要求：不能覆盖浏览器自带快捷键功能 ctrl+a/c/v 等

3.支持热键提示的面板唤醒。要求：输入框中长按功能键（ctrl）唤醒面板，快速按下热键时则不需要唤醒

4.唤醒提示面板后可以按上下键切换选择，enter 键确认输入

请考虑扩展性，如果后续需要做 ctrl+shift+\*,需要符合开闭原则

示例：

设置 ctrl+1 = ‘hello’, 在输入框中同时按下 ctrl+1,输入框中输入 hello

提示面板示例如下 gif 图

![](./1662541403896.gif)

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
