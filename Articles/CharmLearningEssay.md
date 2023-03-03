# Charm 学习笔记

&emsp;&emsp;Charm 是一个致力于构建完美的终端生态的团队，"We build tools to make the command line glamorous."该团队提供了大量使用 Go 开发 CLI/TUI 程序的工具库，如 “前端”框架[Bubble Tea](https://github.com/charmbracelet/bubbletea)、“后端”框架[Charm](https://github.com/charmbracelet/charm)、“移动端”框架[Wish](https://github.com/charmbracelet/wish)等……

> - Charm 官方网站：https://charm.sh
> - Charm GitHub 主页：https://github.com/charmbracelet

## BubbleTea

&emsp;&emsp;Bubble Tea（珍珠奶茶）是一个构建终端应用界面的 Go 框架，采用了 [The Elm Architecture](https://guide.elm-lang.org/architecture/)（Elm 架构）的思想。

> - Bubble Tea GitHub 主页：https://github.com/charmbracelet/bubbletea
> - 官方文档：https://pkg.go.dev/github.com/charmbracelet/bubbletea

### Elm 架构

&emsp;&emsp;Elm 架构的三个重要组成部分是 Model、View 和 Update，Model 记录程序当前状态，View 根据 Model 生成展示给用户的界面，Update 指用户的行为使 Model 的状态发生转变。

### 简单程序

&emsp;&emsp;安装 Bubble Tea 包：

```bash
go get -u github.com/charmbracelet/bubbletea
```

&emsp;&emsp;引入依赖：

```go
import tea "github.com/charmbracelet/bubbletea"
```

&emsp;&emsp;一个 Bubble Tea 程序的主要结构是一个实现了`tea.Model`接口的结构体，它包括三个方法：

```go
type Model interface {
    Init() Cmd
    Update(Msg) (Model, Cmd)
    View() string
}
```

&emsp;&emsp;其中`Init()`方法在程序开始运行时被调用，其返回值是一个“消息”（`tea.Cmd`），会被自动执行。

&emsp;&emsp;`Update(tea.Msg)`方法在用户产生操作后调用，返回更新后的 Model （`tea.Model`）和一条“消息函数”（`tea.Cmd`）。

&emsp;&emsp;`View()`用于生成展示给用户的界面，在调用`Update()`后被自动调用。`View()`的返回值是一个字符串，每次调用`View()`会清除程序的所有输出并输出其返回值。

&emsp;&emsp;在主函数中使用`tea.NewProgram()`方法初始化程序并调用其`Run()`方法运行：

```go
m := model{} 
p := tea.NewProgram(m)
if _, err := p.Run(); err != nil {
    fmt.Printf("Alas, there's been an error: %v", err)
    os.Exit(1)
}
```

> 0. `Update()`返回`tea.Quit`会使程序正常退出
> 1. 程序被 kill 会返回`"program was killed"`错误

### 消息

&emsp;&emsp;消息是实现了`tea.Msg`接口的对象，在发生IO操作时产生。消息可分为以下几种：

- 键盘消息（`tea.KeyMsg`）
- 鼠标消息（`tea.MouseMsg`）
- 批处理消息（`tea.BatchMsg`）
- 窗口大小消息（tea.WindowSizeMsg）

&emsp;&emsp;通常使用`msg := msg.(type)  `获取消息类型并使用 switch case 进行处理，如：

```go
switch msg := msg.(type){
case msg.KeyMsg:
    // ...
case tea.MouseMsg:
    // ...
}
```

#### 键盘消息

&emsp;&emsp;键盘消息（`type KeyMsg Key`）包含用户键盘输入的信息，通常使用其`String()`方法获取输入值：

```go
switch msg.String(){
case tea.KeyCtrlC.String(), "q":
    return m, tea.Quit
}
```

&emsp;&emsp;按键类型（`type KeyType int`）使用一个 int 表示不同的按键，而按键名称（tea.KeyName）是一个`map[KeyType]string`，保存了常用按键的名称，用于上文中的 case 值。

#### 鼠标消息

&emsp;&emsp;鼠标消息（`type MouseMsg MouseEvent`）包含用户鼠标对程序操作的信息。可将其强转为`tea.MouseEvent`后使用其`String()`方法，但效果不咋地。通常直接分析 MouseMsg 的属性字段。

&emsp;&emsp;类似 KeyType ，` type MouseEventType int`保存了鼠标消息的类型。

> 0. 要想获取鼠标消息，首先要启用鼠标

#### 窗口大小消息

&emsp;&emsp;窗口大小消息在程序开始运行时产生一次，在窗口大小发生改变时产生。

> 0. MS Windows 不支持 SIGWINCH 信号，因此不支持窗口大小消息

#### 批处理消息与Cmd

&emsp;&emsp;`tea.Cmd`是一类参数列表为空、返回值为`tea.Msg`的函数，用于生成消息。`type BatchMsg []Cmd`是一组 Cmd，可传输批量执行的 Cmd，但不保证顺序。

&emsp;&emsp;可使用`tea.Batch()`函数不保证顺序地发送 Cmd，也可使用`tea.Sequence()`保证顺序地发送 Cmd。
