在Api31之前，ANR由**ProcessRecord** 的**appNotResponding**方法发送

31之后 交给 **AnrHelper**的**appNotResponding**



## ANR触发

### BroadCastReceiver（10s ------ backend 60s）

```java
queue.scheduleBroadcastsLocked()@ActivityManagerServicce
	--> mHandler.sendMessage(mHandler.obtainMessage(BROADCAST_INTENT_MSG, this)); //BroadCastQueue
		-->processNextBroadcast(true);
			-->processNextBroadcastLocked()
                --> broadcastTimeoutLocked() //发送delay延时消息BROADCAST_TIMEOUT_MSG
```



### Service（front-20s   backend-200s）

```java
-->realStartServiceLocked()@ActiveServices.java
   -->bumpServiceExecutingLocked(r, execInFg, "create"); //发送delay延时消息
	  -->scheduleServiceTimeoutLocked(r.app);
		 -->mAm.mHandler.sendMessageAtTime(msg,proc.execServicesFg ?  //当超时后仍没有remove该SERVICE_TIMEOUT_MSG消息，则执行service Timeout流程
            (now+SERVICE_TIMEOUT) : (now+ SERVICE_BACKGROUND_TIMEOUT));
```



### ContentProvide.publish（10）

```java
attachApplicationLocked()@AMS //在AMS中
    --> mHandler.sendMessageDelayed(msg, CONTENT_PROVIDER_PUBLISH_TIMEOUT); //发送delay延时消息
```



### DispatchTouchEvent（5s）

```c++
InputManager::start()
    -->mDispatcher->start();
    	-->InputDispatcher.cpp --> start() --> dispatchOnce()
    		-->onAnrLocked()  //触发ANR
            	--> mPolicy->notifyAnr() // com_android_server_input_InputManagerService.cpp
            		--> notifyAnr()@InputManagerCallback.java
            			-->notifyANRInner()
            				-->mService.mAmInternal.inputDispatchingTimedOut()// AMS 触发ANR
            
```

