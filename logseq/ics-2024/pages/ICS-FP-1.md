# 善事利器
- 本文旨在介绍一些能提升今后日常学习与工程开发中生活质量的技能，具体是：Nix 能用来做什么、与 Nix 相关的生态现状、Nix 的核心思想，与有关 Nix 的一些常见问题。
- 介绍生态现状与常见问题意图描述一个较为现代的 Nix 入门指南，并提供一些找到最佳实践的思路；本文重点描述一个简洁的理论模型：用「语法树 - 解释器」设计模式来解读有关 Nix 的设计概念。
- ## Nix 能用来做什么
	- 一些常见的问题形如：
		- > Nix 能用来做什么？
		- > 使用 Nix 是浪费时间吗？
		- > Nix 是否不适用于个人用户的场景，而是更加适用于有很多台服务器的公司实验室等场景？
			- 否，都适合
		- > Nix 是否无法运行其他 Linux 发行版可以运行的 / 所编译的软件？
			- 否
	- **Nix 是一个软件包管理器**
		- 有类似 pacman、apt、dnf 等传统 Unix 包管理器的一切功能
		- 并且没有传统系统包管理器的缺点：
			- 对用户
				- 不会出现版本兼容冲突问题
				- 允许安装同一个软件包的多个版本、或有不同补丁的同一个软件版本
				- 修复一个损坏的软件很容易
				- 易于添加官方软件仓库没有的软件源与软件镜像，且不会与官方软件源和已有系统软件发生冲突
				- 很容易获取最新最先进的软件技术，且不会与官方软件源和已有系统软件发生冲突
					- [Chaotic's Nyx](https://github.com/chaotic-cx/nyx)
					- ……
				- ……
			- 对软件打包开发者
				- 易于搭建软件镜像与缓存服务器
				- 构建结果是可复现的，再也不会出现 it works on my machine
				- 最易用的打包时沙箱机制，与可能是最简单的打包流程
				- 可以自由安装软件补丁
				- 配置开发环境、集成测试环境、部署环境、发布打包环境的代码是同一套代码，不需要编写和分别维护多套配置与构建脚本
				- ……
		- 有着全世界最大的软件仓库 nixpkgs
			- [有超过 80000 个软件包](https://search.nixos.org/packages)
			- 拓展和定制 nixpkgs 很容易
				- https://nixos.org/manual/nixpkgs/unstable/#overlays
					- [rust-overlay: Pure and reproducible nix overlay of binary distributed rust toolchains](https://github.com/oxalica/rust-overlay)
					- ……
		- 同时具有二进制发行版和源码发行版的优点，并且没有继承它们分别特有的缺点
			- 二进制发行版：如 Arch Linux、Debian、Fedora 等
				- 使用简单，不需要多少算力和用户时间：不需要编译软件包和软件依赖、只需要从最近的镜像站下载软件、有简单易用的包管理器命令行 / 图形化用户界面
				- ……
			- 源码发行版：如 Gentoo、Linux From Scratch 等
				- 可以按照自己的需求定制软件（甚至系统内核等核心组件）
					- 修改软件的构建脚本和构建参数
					- 修改软件的源码版本和依赖版本
					- 可以自由安装补丁
					- ……
	- **Nix 是一个软件构建工具**
		- 声明式的依赖管理
		- 声明式的开发环境配置
		- 声明式的构建脚本编写
		- 可复现的软件构建
		- 免费的增量构建与缓存，以及依赖关系分析
		- 易用的跨平台构建
		- 易用的分布式构建
		- 易用的构建缓存与镜像系统
		- 易用的软件包发布机制
	- **NixOS 是一个声明式的 Linux 发行版**
		- 声明式的系统服务控制
		- 声明式的系统软件管理
		- 声明式的系统设置配置
		- （除了 NixOS 作为 Linux 发行版存在之外，也有一些别的用于配置操作系统的 Nix 生态项目）
			- [NixOS-WSL](https://github.com/nix-community/NixOS-WSL) for Windows users
			- [nix-darwin](https://github.com/LnL7/nix-darwin) for macOS users
			- [NixBSD](https://github.com/nixos-bsd/nixbsd) NixOS for (Free)BSD hackers
			- [GNU Guix](https://guix.gnu.org/) for GNU hackers
			- [system-manager](https://github.com/numtide/system-manager) for managing system config using Nix on any distro
			- ……
	- **Nix 语言是一门纯函数式的程序设计语言**
		- 所有函数都是纯的、数学意义上的函数
		- 声明式地解决问题，没有控制流原语：如 `for`、`while` 循环，`while`、`break`、`continue`、`return` 语句，异常、恢复等程序设计控制流操作，也没有赋值和可变量的概念
- ## 有关 Nix 的入门建议
	- 目前（2024 年 3 月）最好的
		- Nix 安装工具
		- Nix GitHub Actions 集成
		- Nix 生态的入门教程与概览
	- 在 [Zero to Nix](https://zero-to-nix.com/) 和 [Determinate Systems 开发的开源项目](https://determinate.systems/oss/)。
	- 不要使用 Nix channels、`nix-env` 与大部分形如 `nix-cmd` 的 Nix 命令
		- Nix Flakes 已经是 Nix 生态的事实标准
		- 大部分 `nix-cmd` 系列命令已经有了不带 `-` 符号的更好替代，例如：
			- `nix-build` 被 `nix build` 命令所替代
			- `nix-shell` 被 `nix develop` 与 `nix shell` 命令所替代
		- `nix.channel.enable = false;`
	- 加速 Nix 相关工具的网络连接一般需要配置两个选项：
		- 配置软件镜像，通过镜像加速已经构建好内容的下载
			- 配置 Nix binary cache substituters
				- 通过该选项配置与 Nix store、分发的二进制有关的镜像
				- 例如 `substituters = https://mirrors.bfsu.edu.cn/nix-channels/store/ https://mirror.sjtu.edu.cn/nix-channels/store/ https://cache.nixos.org/`，具体参见各 Nix 镜像站与 binary cache 托管服务商文档
				- https://nixos.org/manual/nix/unstable/command-ref/conf-file.html?conf-substituters#conf-substituters
			- 配置 Nix channels，具体参见各 Nix 镜像站与 binary cache 托管服务商文档；如果已经设置了 disable Nix channel 功能则无需配置
		- 配置 nix-daemon 网络代理，用于加速构建过程中获取源码与 binary cache 的下载
			- 临时：使用 `systemctl edit --runtime` 来编辑 nix-daemon 服务的环境变量
			- 永久：配置 systemd（或 launchd）控制的 nix-daemon 服务中有关网络代理的环境变量
				- ```
				  systemd.services.nix-daemon.environment = {
				    https_proxy = "http://127.0.0.1:7995";
				    http_proxy = "http://127.0.0.1:7995";
				  };
				  
				  launchd.daemons.nix-daemon.serviceConfig.EnvironmentVariables.https_proxy = "http://127.0.0.1:9520";
				  launchd.daemons.nix-daemon.serviceConfig.EnvironmentVariables.http_proxy = "http://127.0.0.1:9520";
				  ```
	- NixOS 默认的安装器镜像内核版本可能不包含一些设备的驱动，例如无线网卡等；建议先编写并构建一个定制的 Live CD 镜像后，再进行烧录与安装；在后文有具体的修改与构建教程。
	- 工具 [nix-output-monitor](https://github.com/maralorn/nix-output-monitor) 可用于可视化 `nix build` 等过程具体发生的步骤。
- ## 例子：使用 Nix 生态的项目开发工作流
	- 通过 git clone 项目到本地，使用 `nix develop` 命令进入开箱即用的开发环境；在开发环境中有所有项目开发过程中所需的依赖、shell hooks、环境变量等内容都已自动配置完成，并且对于同样的操作系统和处理器架构，所有依赖的 hash 值都是相同的，且对于同样的源码，构建产物的 hash 值也都是相同的。这意味着，由于参与项目所有开发者的开发环境都**完全相同**，不会再出现 it works on my machine 之类的情况。
	  logseq.order-list-type:: number
		- 也不需要手动安装和配置**任何**项目依赖与环境变量，在任意新设备上都能以最快速度一键恢复易用的项目工具链与工作流。
	- 将项目提交到 GitHub。提交操作自动触发 Garnix CI，自动构建并缓存开发环境配置与项目所有构建目标的构建产物。稍后在本地运行 Nix 命令时，便会自动按需拉取 Garnix CI 构建的缓存。Nix 的特性确保了这样的「缓存 - 拉取」过程既不会出现缓存未命中，也不会出现使用错误缓存的情况。
	  logseq.order-list-type:: number
	- 项目在发起 merge request 等待合并阶段时，运行项目检查 CI。由于 Nix 项目天生是增量构建的与 Garnix CI 在开发过程中持续的构建缓存集成，CI 在检查过程中不需要运行无用的构建步骤，保持了开发流水线的速度。
	  logseq.order-list-type:: number
	- 使用 Nix 构建的程序可以通过[一行函数调用](https://nix.dev/tutorials/nixos/building-and-running-docker-images.html)包装为 OCI 容器镜像。程序的发布和部署变得容易，并且镜像 layers 的组织相对更加轻量与可复用。
	  logseq.order-list-type:: number
		- 除此之外，Nix 有许多与 OCI、containerd 有关的项目，例如：
			- [Arion](https://docs.hercules-ci.com/arion/)
			- [nix-snapshotter](https://github.com/pdtpartners/nix-snapshotter)
			- [Nixery](https://nixery.dev/)
		- 等，此外最近也有一些有关 Nix 与容器化的演讲，例如：
			- [Nix is a better Docker image builder than Docker's image builder](https://xeiaso.net/talks/2024/nix-docker-build/)
		- 等。有关 Nix 与容器化的最佳实践也是需要单独总结的话题，难以在此处列出和比较特点。
	- 代码合并后，使用 [Colmena](https://github.com/zhaofengli/colmena) 或其他部署工具，将服务持续集成部署到搭载 NixOS 的服务器上。
	  logseq.order-list-type:: number
		- 同样，有关 NixOS 的服务部署也是需要单独总结的话题，难以在此处列出和比较特点。还有一些实用的 NixOS 部署工具，例如：
			- [nixos-anywhere](https://github.com/nix-community/nixos-anywhere)
			- [nixos-infect](https://github.com/elitak/nixos-infect)
			- ……
	- 这些例子旨在体现使用 Nix，可以使用极简的、模块化的、可复用的声明式配置，来解决开发全周期的各类问题。并且，它们都体现出了使用 Nix 的
		- 开箱即用
		- 可复现性
		- 速度快
	- 下面是一个使用 Nix 开发的、monorepo 的前后端软件工程项目所使用的 GitHub Actions 配置，包含了
		- 项目的环境与依赖配置
		- 项目的构建、代码检查与测试
		- 项目的 OCI 容器镜像构建与发布缓存
	- ```
	  on:
	    push:
	      branches: [main]
	    pull_request:
	  jobs:
	    build-and-check:
	      runs-on: ubuntu-latest
	      steps:
	        - uses: actions/checkout@v4
	          with:
	            token: ${{ secrets.TOKEN }}
	            submodules: "recursive"
	  
	        - uses: DeterminateSystems/nix-installer-action@main
	          with:
	            extra-conf: |
	              accept-flake-config = true
	        - uses: DeterminateSystems/magic-nix-cache-action@main
	        - uses: DeterminateSystems/flake-checker-action@main
	  
	        - run: |
	            nix develop --command make init-services
	            nix develop --command make
	            git diff --exit-code
	  ```
- # Theory of Everything About Syntax Trees and Interpreters: No Magic Behind Nix (and NixOS)
- Nix is a tool that takes a unique approach to package management and system configuration. Some of the famous features of Nix and projects using Nix are: reproducible, declarative, reliable, fast at every stage of development and deployment, and more.
- According to my develop experience, one can literally do "every things" about software engineering using Nix, and the reason we would love to get things done in the Nix way is we can always reuse the Nix modules, write less code, configurations and boilerplate, get the deterministic results, and of course, no more "it works on my machine".
- We describe all kinds of software engineering solutions in terms of the Nix programming language, which is purely functional, solve problems in a declarative manner. The powerful abilities and abstractions are driven by merely two simple, minimal and elegant constructions in the Nix language, that is:
	- the **attribute set**s; and
	- the **lambda**s.
- Correspondingly, the beautiful theory that bridging the minimal, elegant terms to the real-world, complicate ones can be explained in theory of two objects:
	- the **syntax tree**s; and
	- the **interpreter**s.
- The following contents demonstrate how to use the "syntax tree - interpreter" pattern to explain how Nix is used to configure and build different kinds of targets, for example, software package and NixOS system closures. We will also cover some frequently asked questions about the NixOS system configuration, and explain **what is and why Nix Flakes**, also using this "syntax tree - interpreter" pattern.
- ## The Attribute Sets
	- In Nix language, the data type attribute sets is like JSON: an attribute set is a collection of name-value-pairs (called attributes) enclosed in curly brackets.
		- ```
		  {
		    x = 123;
		    text = "Hello";
		    y = {
		      bla = 456;
		      z = "yu";
		    };
		    xs = [0 42 19];
		  }
		  ```
	- It can express a little more things than JSON: the value in an attribute set can be a lambda.
	- The Nix language aims at **manipulating attribute sets**, there are some operations:
		- attributes can be accessed with the `.` operator
		- the `//` operator, where expression `xs0 // xs1` returns an attribute set which have of all the attributes in `xs0` and `xs1`. If an attribute name is present in both, the attribute value from the latter is taken.
			- (not mention the `__functor` attribute here, sorry, since we have not introduce the lambda)
	- This is nearly all about attribute sets in the Nix language. You can play with them in the `nix repl`. If you are first to the Nix language, the Nix language manual and other material will introduce more about the attribute sets. For example:
		- `rec` keyword
		- `inherit` keyword
		- `with` keyword
- ## The $\lambda$s
	- The lambdas is a neat way to express the concept of computation, also a convenient tool to to define pure, mathematical functions. The name lambda is directly taken from the underlaying theorem foundation *untyped lambda calculus*.
	- Like attribute sets, the lambdas is also a power tool with a few rules to define it (You can also play with them in the `nix repl`):
		- (function abstraction, define a function)
			- `f = x : x + 42`
				- `x : x + 42` is a lambda (also a ordinary expression in Nix language)
				- `f = x : x + 42` states the identifier `f` is defined to be the abbreviation of the lambda `x : x + 42`. (referential transparency) When reasoning about Nix programs, the occurrence of term `f` can always be substituted by `x : x + 42`, vice versa, which works as any other mathematical functions.
		- (function application)
			- consider a function `f`, or `x : ...` where `...` is a place holder for any expression may or may not contains the occurrences  of variable `x`
			- consider a term `x`, or any other more complex construction that is a valid Nix expression
			- put them in juxtaposition, `f x` (or more complex construction, exactly the same thing by referential transparency, or to say, one can always represent complex constructions in simple terms by first naming it, then substitute) stands for a *function application*; the actually computation part is called *reduction*. Consider the following example:
			- ```
			  nix-repl> f = x : x + 42
			  
			  nix-repl> f 0
			  42
			  
			  nix-repl> g = x : "x" + x
			  
			  nix-repl> g "y"
			  "xy"
			  ```
	- The above two rules nearly all about lambdas in the Nix language.
- ## Put The Two Together: The Nix Programming Langauge
	- Everything in the Nix programming language are exactly one of the three kind of terms: attribute set, lambda, or primitive type values (booleans, strings, numbers, ...). We can say, **everything in the Nix language is about attribute sets and lambdas**.
	- The attribute sets are used to represent simple or complex **structure**s, while the lambdas are used to represent simple or complex **computation**s. And they extends the expressive power of another one each other.
	- use lambdas to extend attribute sets:
		- recalls that attribute sets can express a little more things than JSON: the value in an attribute set can be a lambda
		- if a attribute set contains a attribute named `__functor`, whose value is callable, can be applied as if it were a function, with the set itself passed in first, which can be used to attach metadata to a function without the caller needing to treat it specially
			- ```
			  nix-repl> let
			              add = {__functor = self: x: x + self.x;};
			              inc = add // {x = 1;};
			            in
			              inc 1
			  2
			  ```
		- if a attribute set contains a attribute named `__toString`, whose value is called string-interpolatable, can be used in string interpolation.
			- there is also a `outPath` for similar usages, the difference is:
				- `__toString : Self -> String`
				- `outPath : String`
		- ......
	- use attribute sets to extend lambdas:
		- From the rules for lambdas we stated earlier, we can express functions with multiple arguments in form of `f = x : y : z : ...`, and represent their function application (or partial application) in form of `f x y z`, but what about:
		- named arguments
			- The applications that takes multiple may suppose to be modified, to have a clear code structure, it is needed to have the ability to pass all arguments required by a function at once, and even better, give each argument semantic names and required them to be annotated when constructing function applications. We use attribute sets to express such two requirements.
			- `f = x: y: z: (x + y * z + 42) * y + y * (x - z)`
				- `f 13 24 0`
			- `f = { x, y, z } : (x + y * z + 42) * y + y * (x - z)`
				- `f { x = 13; y = 24; z = 0; }`
		- optional arguments
			- if a function has multiple arguments, one would sometimes hope some of the arguments are optional, that is, if the specify value of some arguments are not given, there are default values for missing ones. We also use attribute sets to express the requirement.
			- `f = { x, y ? 0, z ? 42 } : (x + y * z + 42) * y + y * (x - z)`
				- `f { x = 1; }`
				- `f { x = 13; y = 24; z = 0; }`
		- arguments ignore wildcard
			- it is common #TODO
		- ......