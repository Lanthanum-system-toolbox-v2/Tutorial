# Java模块编写教程
#### Java模块具有真正的强拓展性，可以方便的制作一般功能性模块难以(人性化的)实现的功能
## 准备
- 环境[Idea](https://www.jetbrains.com/idea/) （其实什么ide都行，就连aide也行，只不过需要一些hackly methods）
- [LModule.jar](https://github.com/Lanthanum-system-toolbox-v2/LModule/raw/LModule/out/artifacts/LModule_jar/LModule.jar)
- Java环境（推荐[jdk8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)）
- 编程语言其实是不限的，只要能最终生成可运行的*.jar就可以
## 开始
- 新建项目
- 导入LModule.jar作为库，并且将Scope设置为`Provided`(重要)。不需要额外导入android.jar(已经包含)
- 创建类，比如Test.java
- 将这个类继承LModule接口(`public class Test implements LModule{}`)，并且重写需要重写的方法（点public class那一行然后ALT+ENTER，选择`Implement methods`，然后点ok）
- 撸代码
- [Build Artifacts](https://blog.csdn.net/aspkhdp/article/details/79573196) 来导出jar
- 使用[Class2dex](https://github.com/Lanthanum-system-toolbox-v2/Class2dex.git)工具转换为可被加载的模块
## Demo
- 里面包含了目前sdk提供的所有东西的介绍（不包含android sdk部分)
```
import android.content.Context;
import android.view.View;
import android.widget.LinearLayout;
import android.widget.TextView;
import xzr.La.systemtoolbox.modules.java.LModule;
import xzr.La.systemtoolbox.ui.StandardCard;
import xzr.La.systemtoolbox.utils.process.ShellUtil;

public class TestModule implements LModule{

    @Override
    public String classname() {
        return "cpua";//决定在哪个页面出现，页面列表见功能型模块教程
    }

    @Override
    public View init(Context context) {
        //这个方法决定模块的布局

        LinearLayout linearLayout=new LinearLayout(context);
        linearLayout.setOrientation(LinearLayout.VERTICAL);

        TextView title=StandardCard.title(context);//获取标准的大标题
        title.setText("测试模块");
        linearLayout.addView(title);

        TextView subtitle=StandardCard.subtitle(context);//获取标准的小标题
        subtitle.setText("列出/system目录下的所有内容");
        linearLayout.addView(subtitle);

        TextView textView= new TextView(context);
        textView.setText(ShellUtil.run("ls /system",true));//一个标准执行shell的方法，第一个参数为命令，第二个参数为是否使用root权限，返回类型为string
        linearLayout.addView(textView);

        return linearLayout;

    }

    @Override
    public String onbootapply() {
        return "echo hello";//开机时应用被选中时将调用这个方法来获取应该在开机时干什么 return的内容为shell命令
    }
}
```
![效果](https://raw.githubusercontent.com/Lanthanum-system-toolbox-v2/Tutorial/master/tutorial/res/photo_2019-01-14_14-34-11.jpg)
