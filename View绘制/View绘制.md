在ViewRootImpl创建并绑定前，View不会执行任何绘制。

当windowManager添加View的时候，RV会被创建，并绑定decor，然后开始绘制View

所以在生命周期中调用View.post 会将消息存入HandlerActionQueue，当RV执行performTraversals时，会执行dispatchAttachedToWindow，此时处理该队列。





WindowManagerImpl：确定 View  属于哪个屏幕，哪个父窗口
WindowManagerGlobal：管理整个进程 所有的窗口信息
ViewRootImpl：WindowManagerGlobal 实际操作者，操作自己的窗口

## ViewRootImpl.setView
​	--> requestLayout(); // 请求遍历
​		--> scheduleTraversals
​			--> mChoreographer.postCallback(
​                    Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);
​				--> doTraversal
​					--> performTraversals(); // 绘制View 
​	--> res = mWindowSession.addToDisplayAsUser // 将窗口添加到WMS上面  WindowManagerService
​	--> 事件处理
​	--> view.assignParent(this); // getParent  ViewRootImpl

## ViewRootImpl 构造方法
mThread = Thread.currentThread(); // 拿到创建它的线程，MainThread --- 默认
mDirty = new Rect(); // 脏区域 
mAttachInfo = new View.AttachInfo(); // 保存当前窗口的一些信息





## performTraversals@ViewRootImpl.java
​	--> windowSizeMayChange |= measureHierarchy(); // 预测量
​	--> relayoutResult = relayoutWindow(params, viewVisibility, insetsPending); // 布局窗口
​	--> performMeasure(childWidthMeasureSpec, childHeightMeasureSpec); // 控件树测量
​	--> performLayout(lp, mWidth, mHeight); // 布局
​	--> performDraw(); // 绘制



## 三次测量 --- 

1. 设置一个值，进行第一次测量，MEASURED_STATE_TOO_SMALL

2. 获取一个状态值

3. 改变大小 baseSize = (baseSize+desiredWindowWidth)/2;

4. 进行第二次测量

5. 如果还不满意，直接给自己的最大值，然后第三次测量 -- 不确定
  如果 windowSizeMayChange = true; --》 表示还需要测量

  ### 第二种解释

  1、第一次：先用预置宽度测量ViewTree，得到测量结果。

  2、发现第一次测量结果不满足，因为存在需要宽度比预置宽度大的子布局，于是给子布局更大的宽度，再进行第二次测量。

  3、发现第二次测量结果仍然不满足，于是用Window能够拿到的最大宽度再进行测量。