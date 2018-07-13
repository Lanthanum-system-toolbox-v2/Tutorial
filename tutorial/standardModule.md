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

### 组件们
- 以下内容将会介绍各种组件的用法
#### title与subtitle
<pre><code>!module
class=xxx
  !view
  view=title/subtitle
  text=标题文字
  @show
  #return sth
  @show
  root=true/false
  core=true/false
  !view
!module
</code></pre>
- 私有属性只有一个text，不多介绍
#### switch开关
<pre><code>!module
class=xxx
  !view
  view=switch
  text=启用（emm想写什么都行）
  @show
  #return sth
  @show
  @click
  #此处提供变量$switch来显示当前switch状态
  if [ $switch == 1 ]
  then
  #显示toast弹窗
  echo @toast=开关是开启的
  else
  echo @toast=开关是关闭的
  fi
  @click
  @current
  #当前状态设置为打开状态
  echo true
  @current
  root=true/false
  core=true/false
  !view
!module
</code></pre>
- 私有属性：click,current
#### button按钮
<pre><code>!module
class=xxx
  !view
  view=button
  text=按钮文字
  @show
  #return sth
  @show
  @click
  $do sth
  @click
  root=true/false
  core=true/false
  !view
!module
</code></pre>
- 很简单 click
#### edittext文本编辑框
<pre><code>!module
class=xxx
  !view
  view=edittext
  text=文本框：
  @show
  #return sth
  @show
  @current
  echo 我是当前内容内容
  @current
  @done
  #提供变量$edittext
  echo @toast=你输入了$edittext
  @done
  width=200
  root=true/false
  core=true/false
  !view
!module
</code></pre>
- done方法，代表在点击勾时的操作。
- width属性。如果定义，则使用定义值作为输入框宽度。如果不定义，则使用默认值来作为宽度。（单位是dp，无需写上）
#### spinner类似于处理器一般配置里的频率选择下拉菜单
<pre><code>!module
class=xxx
  !view
  view=spinner
  //定义条目
  item=我是第一条|我是第二条|我是第三条
  #也可以item_file=[path]这是从系统中读取，适用于末尾有空格的类似于处理器频率表文件的解析
  #也可以item_file_tcp=[path]这是从系统中读取，适用于末尾没空格的类似于处理器频率表文件的解析
  #也可以item_file_io=[path]这是从系统中读取，适用于类似于IO调度器的类似格式解析（有兴趣看看IO调度器的那个模块）
  text=选择一个条目：
  @show
  #return sth
  @show
  @current
  #默认选中第二个条目
  echo position=1
  #也可以echo label=条目的文字内容
  #也可以echo io_label=`cat [path]` 方便类IO调度器状态的解析
  @current
  @done
  #提供变量$label与$position
  echo @toast=当前点击了第$position个条目（从0开始算），条目的内容是$label
  @done
  root=true/false
  core=true/false
  !view
!module
</code></pre>
- done属性与current属性提供多变量。
#### seekbar 拖动条
- 直接贴出高通Gpu调度激进程度控制来进行解读
<pre><code>
!module
class=cpua
!view
view=title
text=高通GPU调度配置
@show
echo true
@show
!view

!view
view=subtitle
text=调整高通GPU调度的激进程度
@show
echo true
@show
!view

!view
view=seekbar
//\s可以用来表示空格 你也可以直接打空格
text=《——保守\s\s\s\s\s\s\s\s\s\s\s\s\s\s\s\s\s激进——》
@max
#返回一个数字作为这个进度条的最大值
cat /sys/class/kgsl/kgsl-3d0/min_pwrlevel
@max
@show
#在需要时显示
if [ -e /sys/class/kgsl/kgsl-3d0/default_pwrlevel ]
then
echo true
fi
@show
@current
#当前状态
#这里牵扯到一个小算法 因为gpu调度激进程度和数字时相反的。数字越大反而越不激进。因此我们需要将它倒回来。
r=`cat /sys/class/kgsl/kgsl-3d0/default_pwrlevel`
let c=max-r
echo $c
@current

@done
#我们提供了$max和$seekbar两个变量，分别代表最大值和当前值，方便计算。
#下列算法又需要将它倒回来
#此处千万不能去掉调用前的$ 即使它在bash下可以被去掉
let r=$max-$seekbar
echo $r > /sys/class/kgsl/kgsl-3d0/default_pwrlevel
@done
core=true
root=true
!view
!module

</pre></code>
- 这个组件不算难 没什么可多讲的。
- 还有一个text组件，拥有属性@text。作用是动态显示text。其它与title以及subtitle完全相同。
### 假如你有不清楚的地方，可以去[官方模块仓库](https://github.com/xzr467706992/Lanthanum_system_toolbox_v2/tree/zh_CN)一探究竟。
