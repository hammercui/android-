
#rn0.39填坑

1.  Android版本下Text lineHeight必须为int型
2.  ios版本图片不再支持borderTopLeftRadius等四个角的设定，VIew还支持
3.  image 的resizeMode 模式如何使用

#android填坑

>主要记录在Android开发过程中遇到的问题,解决方案，以及引发的思考。

## 目录
- [ ] [app如何进行崩溃日志收集？](#app如何进行崩溃日志收集)
- [ ] 研究内存泄漏分析
- [ ] Android版本，二维码生成后存储，没有获得权限，如何获得权限
- [x] sdk>=18,如何使用系统自带的图库，进行图片多选？
- [ ] fresco是如何优化图片内存的？
- [ ] android目前最优秀的裁切工具ucrop，是不是得研究下源码？
- [ ] android Intent Action 大全
- [x] [android studio 如何打包library并上传本地仓库，如何上传网络仓库](#android studio 如何打包library并上传本地仓库，如何上传网络仓库)
- [ ] 比较parceso Fresco uil三个图片库的优劣？
- [ ] popwindow的机制，window与view的区别与联系？
- [ ] gradle的task命令如何编写。
- [ ] android打包apk如何加壳？
- [ ] java的强引用，若引用区别？
- [ ] java的classloader(类加载器)，以及使用，比如内粗类实现的单例类？
- [ ] java双检索实现的单例，是线程安全的。
- [ ] jav的synchronize关键字，volatile关键字。
- [ ] [apk如何瘦身](#apk如何瘦身？)



## 正文

###apk如何瘦身？

参考知乎[D-clock爱吃葱花](https://www.zhihu.com/people/d_clock/answers)的[Android应用瘦身，从18MB到12.5MB](https://zhuanlan.zhihu.com/p/25465537)一文。

首先打开Build/Analyze Apk进行apk分析
![image](/image/1.png)


重点讲三个知识点

* 使用 Drawable XML、Color、.9 PNG 代替 PNG。

> 从性能上看，比起使用图片资源需要先将其生成 Bitmap 再传到底层交由 GPU 渲染，用 Drawable XML 和 Color 则更加高效，它是直接将 Shape 信息传到底层由 GPU 进行渲染，CPU 和 内存的占用会更少

* 不到报没有使用的资源，删除没有使用的资源

> 在项目的build.gradle中配置`shrinkResources true `

```
buildtypes {
 
 release{
 	minifyEnable true //是否混淆
 	shrinkResources true //是否去除无用的资源文
 }
}
```

*  删除没有使用的资源 Analyze/Rn Inspection by Name 输入 Unused Resources ，然后查看结果
![image](2.png)


###app如何进行崩溃日志收集
```
 private static final String LOG_DIR = Environment.getExternalStorageDirectory().getAbsolutePath() + "/crash/log/";
    private static  String LOG_NAME ;
    private ArrayList<Activity> list = new ArrayList<Activity>();

    private  MyCatchExceptionHandler handler = null;
    private  static   CrashManage  ins;
    private CrashManage(){};

    public static CrashManage getIns(){
        if(ins == null){
            synchronized (CrashManage.class){
                if(ins == null)
                    ins = new CrashManage();
            }
        }
        return ins;
    }

    public  void init(String appName){
        LOG_NAME = appName +"_"+getCurrentDateString() + ".txt";

        if(handler  ==  null){
            handler = new MyCatchExceptionHandler();
        }
        Thread.setDefaultUncaughtExceptionHandler(handler);
    }



    private class MyCatchExceptionHandler implements Thread.UncaughtExceptionHandler{

        @Override
        public void uncaughtException(Thread thread, Throwable ex) {
            writeErrorLog(ex);
            exit();
        }
    }
    /**
     * 打印错误日志
     *
     * @param ex
     */
    protected void writeErrorLog(Throwable ex) {
        String info = null;
        ByteArrayOutputStream baos = null;
        PrintStream printStream = null;
        try {
            baos = new ByteArrayOutputStream();
            printStream = new PrintStream(baos);
            ex.printStackTrace(printStream);
            byte[] data = baos.toByteArray();
            info = new String(data);
            data = null;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (printStream != null) {
                    printStream.close();
                }
                if (baos != null) {
                    baos.close();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        Log.d("crashlog", "崩溃信息\n" + info);
        File dir = new File(LOG_DIR);
        if (!dir.exists()) {
            dir.mkdirs();
        }
        File file = new File(dir, LOG_NAME);
        try {
            FileOutputStream fileOutputStream = new FileOutputStream(file, true);
            fileOutputStream.write(info.getBytes());
            fileOutputStream.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    /**
     * 获取当前日期
     *
     * @return
     */
    private static String getCurrentDateString() {
        String result = null;
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd",
                Locale.getDefault());
        Date nowDate = new Date();
        result = sdf.format(nowDate);
        return result;
    }

    /**
     * Activity关闭时，删除Activity列表中的Activity对象
     */
    public void removeActivity(Activity a) {
        list.remove(a);
    }

    /**
     * 向Activity列表中添加Activity对象
     */
    public void addActivity(Activity a) {
        list.add(a);
    }

    /**
     * 关闭Activity列表中的所有Activity
     */
    public void exit() {
        for (Activity activity : list) {
            if (null != activity) {
                activity.finish();
            }
        }
        // 杀死该应用进程
        android.os.Process.killProcess(android.os.Process.myPid());
    }
```

###java的classloader(类加载器)，以及使用，比如内粗类实现的单例类？

###java双检索实现的单例，是线程安全的。
```
public class Singleleton {   
   private Singleleton() {}   
   private static Singleleton instance = null;   
   public static Singleleton getInstance() {   
      if(instance == null){    
         synchronize(Singleleton.class){   
            if(instance == null){   
               instance = new Singleton();   
            }   
         }   
      }   
      return instance;   
   }   
}  

```
###android studio如何打包library并上传本地仓库，或者上传网络仓库

使用文件模式管理library，显得既低效又混乱。考虑使用gradle脚本打包librar之后，上传本地maven库获得网络库，
网络库包括:

 1. jcenter远程仓库(公共)，需要注册。
 2. github搭建的远程仓库(公共)
 3. nexus搭建的maven私服。

执行打包上传aar的命令：

```
 ./gradlew -p PImagePicker(你的library的name) clean build uploadArchives -info
```

####本地库
首先确定我们新建的`module`是`Android library`,可以在`module`的`build.gradle`看到`apply plugin: 'com.android.library'`
新增如下

```
apply plugin: 'com.android.library'
apply plugin: 'maven'
```
新增打包脚本：

```
uploadArchives {
    repositories {
        mavenDeployer {
            //本地maven
            repository(url: 'file:///Users/cly/Documents/Android/repo/')
            pom.project {
                name '库名,打包命令使用'
                groupId 'com.hammer.anlib'
                artifactId 'PImagePicker'
                version '1.0.0'
                packaging 'aar'

                licenses {
                    license {
                        name 'The MIT License'
                        url 'https://raw.githubusercontent.com/hammercui/android-advance-cn/master/LICENSE'
                        distribution 'repo'
                    }
                }
                developers {
                    developer {
                        id 'hammercui'
                        name 'hammercui'
                        email 'hammercui@163.com'
                    }
                }
            }
        }
    }
}
```

然后执行上文中提到的打包命令，打包完成后我们就可以在`Users/cly/Documents/Android/repo/`目录看到`com/hammercui/anlib/PImagePicker/1.0.0/`等文件，表示打包成功。

**如何引用?**

在工程目录的build.gradle新增maven路径

```
 maven{
      url 'file:///Users/cly/Documents/Android/repo/'        
      }
```

然后在其他module直接引用依赖

```
//+号表示一直饮用最新版本
compile 'com.hammer.anlib:PImagePicker:+@aar'
```


####网络库
私有库，我们一般使用nexus搭建私有仓库。比如搭建的私有库地址是：
`http://maven.****.com/nexus/content/repositories/thirdparty`
我们只需要在打包文件里替换下

```
//私有maven
repository(url:  'http://???'){
                authentication(userName: '账号', password:'密码')
}
            
```  

或者使用github提供的公共免费maven库，具体使用方法与私有类似。

          

###需求分析





# MIT License

Copyright (c) 2017 hammercui

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.














