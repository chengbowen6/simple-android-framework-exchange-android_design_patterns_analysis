本文为 Android设计模式源码中 装饰模式 分析  
Android系统版本： 5.0  
分析者：Tiny-Times，分析状态：未完成，校对者：无，校对状态：未开始

## 1\. 模式介绍

* * *

### 模式的定义

    在不必改变原类文件和使用继承的情况下，动态地扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。

### 模式的使用场景

*   需要扩展一个类的功能，或给一个类添加附加职责。
*   需要动态的给一个对象添加功能，这些功能可以再动态的撤销。
*   需要增加由一些基本功能的排列组合而产生的非常大量的功能，从而使继承关系变的不现实。
*   当不能采用生成子类的方法进行扩充时。一种情况是，可能有大量独立的扩展，为支持每一种组合将产生大量的子类，使得子类数目呈爆炸性增长。另一种情况可能是因为类定义被隐藏，或类定义不能用于生成子类。

## 2\. UML类图

* * *

![装饰模式标准类图][1]

### 角色介绍

*   Component:定义一个对象接口，可以给这些对象动态地添加职责。
*   ConcreteComponent:是要动态扩展的对象，它继承自Component。
*   Decorator:维持一个指向Component对象的指针，该指针指向需要被装饰的对象；并定义一个与Component接口一致的接口。
*   ConcreteDecorator:向组件添加职责。

## 3\. 该模式的实现实例

**Component：定义一个对象接口，可以给这些对象动态地添加职责。**

    public interface Component
    {
        void operation();
    }
    

**Concrete Component：定义一个对象，可以给这个对象添加一些职责。**

    public class ConcreteComponent implements Component
    {
        public void operation()
        {
            // to  do  sth here
        }
    }
    

**Decorator：维持一个指向Component对象的引用，并定义一个与 Component接口一致的接口。**

    public class Decorator implements Component
    {
        public Decorator(Component component)
        {
                this.component = component;
        }
    
        public void operation()
        {
                component.operation();
        }
    
        private Component component;
    }
    

**Concrete Decorator：在Concrete Component的行为之前或之后，加上自己的行为，以“贴上”附加的职责。**

    public class ConcreteDecorator extends Decorator
    {
        public void operation()
        {        
                super.operation();            
                addBehavior();
        }
    
        private void addBehavior()
        {
              //to  do  sth here
         }
    }
    

### 装饰模式在Android源码中的应用

**在Android源码中，其中使用到装饰模式的就是由Context ,ContextWrapper等设计。继承结构如下图所示：** ![装饰模式android中应用类图][2]

### 角色介绍

*   Context就是我们的抽象组件，它提供了应用运行的基本环境，是各组件和系统服务通信的桥梁，隐藏了应用与系统服务通信的细节，简化了上层应用的开发。所以Contex就是“装饰模式”里的Component。
*   Context类是个抽象类，android.app.ContextImpl派生实现了它的抽象接口。ContextImpl对象会与Android框架层的各个服务（包括组件管理服务、资源管理服务、安装管理服务等）建立远程连接，通过对Android进程间的通信机制（IPC）和这些服务进行通信。所以ContextImpl就是“装饰模式”里的ConcreteComponent。 。
*   如果上层应用期望改变Context接口的实现，就需要使用android.content.ContextWrapper类，它派生自Context，其中具体实现都是通过组合的方式调用ContextImpl类的实例（在ContextWrapper中的private属性mBase）来完成的。这样的设计，使得ContextImpl与ContextWrapper子类的实现可以单独变化，彼此独立。所以可以看出ContextWrapper就是“装饰模式”里的Decorator。
*   Android的界面组件Activity、服务组件Service以及应用基类Application都派生于ContextWrapper，它们可以通过重载来修改Context接口的实现。所以可以看出Activity、服务组件Service以及应用基类Application就是“装饰模式”里的具体装饰角色A、B、C。

### Android源码中的模式实现

* * *

**Context抽象类：在该抽象类中定义了一系列get***()和set***()等抽象函数，其中有一个没有实现的startActivity抽象函数：**

    public abstract class Context {
        public abstract void startActivity(Intent intent);
    }
    

**ContextImpl类：startActivity方法实现**

    class ContextImpl extends Context {
    
    @Override
    public void startActivity(Intent intent) {
        if ((intent.getFlags()&Intent.FLAG_ACTIVITY_NEW_TASK) == 0) {
            throw new AndroidRuntimeException(
                    "Calling startActivity() from outside of an Activity "
                    + " context requires the FLAG_ACTIVITY_NEW_TASK flag."
                    + " Is this really what you want?");
        }
        mMainThread.getInstrumentation().execStartActivity(
            getOuterContext(), mMainThread.getApplicationThread(), null,
            (Activity)null, intent, -1);
       }
    }
    

**ContextWrapper调用startActivity**

      public class ContextWrapper extends Context {
          Context mBase;
    
          public ContextWrapper(Context base) {
            mBase = base;
      }
    

**Context抽象类的实例对象mBase**

      @Override
      public void startActivity(Intent intent) {
         mBase.startActivity(intent);
         //调用父类Context的方法mBase.startActivity(intent)。
      }
    

**具体装饰类如何来装饰和扩展父类ContextWrapper** **Activity类：**

    public class Activity extends ContextThemeWrapper
         implements LayoutInflater.Factory2,
         Window.Callback, KeyEvent.Callback,
         OnCreateContextMenuListener, ComponentCallbacks2 {
       private static final String TAG = "Activity";
    
       private static class ManagedDialog {
           Dialog mDialog;
           Bundle mArgs;
       }
       private SparseArray<ManagedDialog> mManagedDialogs;
       private int mDefaultKeyMode = DEFAULT_KEYS_DISABLE;
       private SpannableStringBuilder mDefaultKeySsb = null;
       protected static final int[] FOCUSED_STATE_SET = {com.android.internal.R.attr.state_focused};
    
       private final Object mInstanceTracker = StrictMode.trackActivity(this);
    
       private Thread mUiThread;
       final Handler mHandler = new Handler();
    
       public Intent getIntent() {
        return mIntent;
       }
    
       public void setIntent(Intent newIntent) {
           mIntent = newIntent;
       }
    
       /** Return the application that owns this activity. */
       public final Application getApplication() {
           return mApplication;
       }
    
       /** Is this activity embedded inside of another activity? */
       public final boolean isChild() {
           return mParent != null;
       }
       /** Return the parent activity if this view is an embedded child. */
       public final Activity getParent() {
          return mParent;
       }
    
       /** Retrieve the window manager for showing custom windows. */
       public WindowManager getWindowManager() {
           return mWindowManager;
       }
    }
    

**Service类**

    public abstract class Service extends ContextWrapper implements ComponentCallbacks2 {
    
        private static final String TAG = "Service";
    
        public Service() {
           super(null);
        }
    
        public final Application getApplication() {
           return mApplication;
        }
    
        public void onCreate() {
        }
    
        @Deprecated
        public void onStart(Intent intent, int startId) {
        }
        public static final int START_CONTINUATION_MASK = 0xf;
    
        public static final int START_STICKY_COMPATIBILITY = 0;
    
        public static final int START_STICKY = 1;
    }
    

## 4\. 注意事项

*   接口的一致性；装饰对象的接口必须与它所装饰的Component的接口是一致的，因此，所有的ConcreteDecorator类必须有一个公共的父类；这样对于用户来说，就是统一的接口。
*   省略抽象的Decorator类；当仅需要添加一个职责时，没有必要定义抽象Decorator类。因为我们常常要处理，现存的类层次结构而不是设计一个新系统，这时可以把Decorator向Component转发请求的职责合并到ConcreteDecorator中 。
*   保持Component类的简单性；为了保证接口的一致性，组件和装饰必须要有一个公共的Component类，所以保持这个Component类的简单性是非常重要的，所以，这个Component类应该集中于定义接口而不是存储数据。对数据表示的定义应延迟到子类中，否则Component类会变得过于复杂和臃肿，因而难以大量使用。赋予Component类太多的功能，也使得具体的子类有一些它们它们不需要的功能大大增大；corator:向组件添加职责 。

## 5\. 杂谈

**优点**

*   装饰类和被装饰类可以独立发展，而不会相互耦合。换句话说，Component类无须知道Decorator类，Decorator类是从外部来扩展Component类的功能，而Decorator也不用知道具体的组件 。

**缺点**

*   这种比继承更加灵活机动的特性，也同时意味着更加多的复杂性 。
*   装饰模式会导致设计中出现许多小类，如果过度使用，会使程序变得很复杂 。

 [1]: http://belial.me/wp-content/uploads/2015/03/QQ%E6%88%AA%E5%9B%BE20150314161540.png
 [2]: http://belial.me/wp-content/uploads/2015/03/QQ%E6%88%AA%E5%9B%BE20150315113334.png