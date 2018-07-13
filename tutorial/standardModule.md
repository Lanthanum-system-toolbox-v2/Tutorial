# 功能型模块编写教程
-这个教程将会教你编写功能型模块，但是你能不能学会就不关我事了.（手动滑稽）
### 基本介绍
- 标准的功能型模块由一个文本文件直接翻译而成，需要一个<code>!module</code>作为头和尾。
- 它将会被加载作为镧·系统工具箱中主要功能(处理器高级配置、io配置等）功能页面的内容
### 模块的基本属性
- 每一个功能型模块都需要定义class属性，定义方法为增加行<code>class=xxx</code>这个属性指定了该功能型模块将被防止到哪个页面。工具箱提供了以下可定义的接口。
- cpua (处理器高级配置)
- io (IO配置)
- extra (杂项配置)
- charge(充电控制)
- selinux (SELinux页面配置）
- 除此之外，模块可以在内部定义view属性，这将在模块内增加UI。
### view属性的定义
<pre><code>!module
class=xxx
!view
view=xxxx
view属性的内容
!view
!module
</code></pre>
- 一个view属性代表一个UI组件。
- 每一个view属性中，需要定义一个view子属性。如上示例所示。
- 子属性的定义内容以及对应参数将在下方讲到。
### view的基本属性
#### 以下将module的view属性在UI上显示的效果简称为“组件”
<pre><code>!module
class=xxx
  !view
  view=xxxx
  
  root=true/false
  core=true/false
  
  @show
  #写一个shell脚本 返回true为显示组件 返回其他任何内容或不返回为不显示组件
  #以下是一个demo脚本 在/system/build.prop存在时返回true
  
  if [ -e /system/build.prop ]
  then
  echo true
  fi
  
  @show
  
  !view
!module
</code></pre>
- 如上述示例所示 你可以通过格式整理来使模块更易阅读
- root属性决定了这个组件的【任何】操作是否调用root权限
- show属性决定了这个组件在什么情况下会显示【所有带shell在中间的属性全部用@xxx来定义】
- core属性是一个【非常重要】的属性。<pre>它是用来决定这个module的卡片是否显示用的。
假如一个模块中有一个带有core=true属性的组件处于显示状态（@show属性返回true)，
那么这个模块就会被显示，否则它将被隐藏。
即使一个模块中所有的组件的@show属性均返回true。但是如果所有的组件都没有core属性=true的定义，
那么这个模块也将不会被显示。
当然，它还决定了组件是否会被读取到【开机时应用】的任务列表中，非core=true的组件永远不会，也没有办法进入开机应用列表。
</pre>

- 引入看似混乱的core属性的作用在于。使模块中一些常显示的内容（如标题）不会影响模块的显示状态。
- 以下这个例子能够让你更加清晰的认识core属性。

<pre><code>!module
//显示在杂项配置中
class=extra
  !view
  //定义标题
  view=title
  text=一键变砖
  @show
  echo true
  @show
  !view
  
  !view
  view=subtitle
  text=点击按钮一键变砖
  @show
  echo true
  @show
  !view

  !view
  //ui中加入的时按钮
  view=button
  
  root=true
  core=true
  
  @show
  #写一个shell脚本 返回true为显示组件 返回其他任何内容或不返回为不显示组件
  #以下是一个demo脚本 在/system/build.prop存在时返回true
  
  if [ -e /system/build.prop ]
  then
  echo true
  fi
  
  @show
  
  @click
  #删除build.prop
  rm -f /system/build.prop
  @click
  
  !view
  
!module
</code></pre>

- 上面这个模块只会在build.prop存在时显示。准确的说，是button带领着整个模块显示的。（即使title和subtitle始终显示)

### 未完待续
