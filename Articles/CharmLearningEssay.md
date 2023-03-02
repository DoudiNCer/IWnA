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

&emsp;&emsp;一个 Bubble Tea 程序的主要结构是一个实现了`bubbletea.Model`接口的结构体，它包括三个方法：

```go
type Model interface {
    Init() Cmd
    Update(Msg) (Model, Cmd)
    View() string
}
```

&emsp;&emsp;其中`Init()`方法在程序开始运行时被调用，其返回值是一个“消息”（tea.Cmd），会被自动执行。

&emsp;&emsp;`Update(tea.Msg)`方法在用户产生操作后调用，返回更新后的 Model （tea.Model）和一条“消息”（tea.Cmd）。

&emsp;&emsp;`View()`用于生成展示给用户的界面，在调用`Update()`后被自动调用。`View()`的返回值是一个字符串，每次调用`View()`会清除程序的所有输出并输出其返回值。

&emsp;&emsp;在主函数中使用`tea.NewProgram（）`方法初始化程序并调用其`Run()`方法运行：

```go
m := model{} 
p := tea.NewProgram(m)
if _, err := p.Run(); err != nil {
    fmt.Printf("Alas, there's been an error: %v", err)
    os.Exit(1)
}
```
