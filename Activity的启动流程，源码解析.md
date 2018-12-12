### 1.最开始调用Activity #startActivity

### 2.最终都是由Activity #startActivityForResult
 	//这里会启动新的Activity，核心功能都在mMainThread.getApplicationThread()中完成  
        Instrumentation.ActivityResult ar =  
            mInstrumentation.execStartActivity(  
                this, mMainThread.getApplicationThread(), mToken, this,  
                intent, requestCode, options);  


### 3.在Instruction的execStartActivity方法中
	public ActivityResult execStartActivity(  
        Context who, IBinder contextThread, IBinder token, Activity target,  
        Intent intent, int requestCode, Bundle options) {  
    //核心功能在这个whoThread中完成，其内部scheduleLaunchActivity方法用于完成activity的打开  
    IApplicationThread whoThread = (IApplicationThread) contextThread;  
    if (mActivityMonitors != null) {  
        synchronized (mSync) {  
            //先查找一遍看是否存在这个activity  
            final int N = mActivityMonitors.size();  
            for (int i=0; i<N; i++) {  
                final ActivityMonitor am = mActivityMonitors.get(i);  
                if (am.match(who, null, intent)) {  
                    //如果找到了就跳出循环  
                    am.mHits++;  
                    //如果目标activity无法打开，直接return  
                    if (am.isBlocking()) {  
                        return requestCode >= 0 ? am.getResult() : null;  
                    }  
                    break;  
                }  
            }  
        }  
    }  
    try {  
        intent.migrateExtraStreamToClipData();  
        intent.prepareToLeaveProcess();  
        //这里才是真正打开activity的地方，核心功能在whoThread中完成。  
        int result = ActivityManagerNative.getDefault()  
            .startActivity(whoThread, who.getBasePackageName(), intent,  
                    intent.resolveTypeIfNeeded(who.getContentResolver()),  
                    token, target != null ? target.mEmbeddedID : null,  
                    requestCode, 0, null, null, options);  
        //这个方法是专门抛异常的，它会对结果进行检查，如果无法打开activity，  
        //则抛出诸如ActivityNotFoundException类似的各种异常  
        checkStartActivityResult(result, intent);  
    } catch (RemoteException e) {  
    }  
    return null;  
}  

要去看看IApplicationThread，因为核心功能由其内部的scheduleLaunchActivity方法来完成，由于IApplicationThread是个接口，实现类ActivityThread中的内部类ApplicationThread
### 4.ApplicationThread#scheduleLaunchActivity.

	public final void scheduleLaunchActivity(Intent intent, IBinder token, int ident,  
        ActivityInfo info, Configuration curConfig, CompatibilityInfo compatInfo,  
        int procState, Bundle state, List<ResultInfo> pendingResults,  
        List<Intent> pendingNewIntents, boolean notResumed, boolean isForward,  
        String profileName, ParcelFileDescriptor profileFd, boolean autoStopProfiler) {  
  
    updateProcessState(procState, false);  
  
    ActivityClientRecord r = new ActivityClientRecord();  
  
    r.token = token;  
    r.ident = ident;  
    r.intent = intent;  
    r.activityInfo = info;  
    r.compatInfo = compatInfo;  
    r.state = state;  
  
    r.pendingResults = pendingResults;  
    r.pendingIntents = pendingNewIntents;  
  
    r.startsNotResumed = notResumed;  
    r.isForward = isForward;  
  
    r.profileFile = profileName;  
    r.profileFd = profileFd;  
    r.autoStopProfiler = autoStopProfiler;  
  
    updatePendingConfiguration(curConfig);  
  
    queueOrSendMessage(H.LAUNCH_ACTIVITY, r);  
}  

上述代码很好理解，构造一个activity记录，然后发送一个消息，所以，我们要看看Handler是如何处理这个消息的，现在转到这个Handler，它有个很短的名字叫做H
### 5.code：ActivityThread#H Handler处理这个
	//这个类太长，我只帖出了我们用到的部分  
	private class H extends Handler {  
  
    public void handleMessage(Message msg) {  
        if (DEBUG_MESSAGES) Slog.v(TAG, ">>> handling: " + codeToString(msg.what));  
        switch (msg.what) {  
            //这里处理LAUNCH_ACTIVITY消息类型  
            case LAUNCH_ACTIVITY: {  
                Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "activityStart");  
                ActivityClientRecord r = (ActivityClientRecord)msg.obj;  
  
                r.packageInfo = getPackageInfoNoCheck(  
                        r.activityInfo.applicationInfo, r.compatInfo);  
                //这里处理startActivity消息  
                handleLaunchActivity(r, null);  
                Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);  
            } break;  
            case RELAUNCH_ACTIVITY: {  
                Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "activityRestart");  
                ActivityClientRecord r = (ActivityClientRecord)msg.obj;  
                handleRelaunchActivity(r);  
                Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);  
            } break;  
            case PAUSE_ACTIVITY:  
                Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "activityPause");  
                handlePauseActivity((IBinder)msg.obj, false, msg.arg1 != 0, msg.arg2);  
                maybeSnapshot();  
                Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);  
                break;  
            ...  
        }  
}  
### 6.ActivityThread#handleLaunchActivity

	private void handleLaunchActivity(ActivityClientRecord r, Intent customIntent) {  
    // If we are getting ready to gc after going to the background, well  
    // we are back active so skip it.  
    unscheduleGcIdler();  
  
    if (r.profileFd != null) {  
        mProfiler.setProfiler(r.profileFile, r.profileFd);  
        mProfiler.startProfiling();  
        mProfiler.autoStopProfiler = r.autoStopProfiler;  
    }  
  
    // Make sure we are running with the most recent config.  
    handleConfigurationChanged(null, null);  
  
    if (localLOGV) Slog.v(  
        TAG, "Handling launch of " + r);  
    //终于到底了，大家都有点不耐烦了吧，从方法名可以看出，  
    //performLaunchActivity真正完成了activity的调起,  
    //同时activity会被实例化，并且onCreate会被调用  
    Activity a = performLaunchActivity(r, customIntent);  
  
    if (a != null) {  
        r.createdConfig = new Configuration(mConfiguration);  
        Bundle oldState = r.state;  
        //看到没，目标activity的onResume会被调用  
        handleResumeActivity(r.token, false, r.isForward,  
                !r.activity.mFinished && !r.startsNotResumed);  
  
        if (!r.activity.mFinished && r.startsNotResumed) {  
            // The activity manager actually wants this one to start out  
            // paused, because it needs to be visible but isn't in the  
            // foreground.  We accomplish this by going through the  
            // normal startup (because activities expect to go through  
            // onResume() the first time they run, before their window  
            // is displayed), and then pausing it.  However, in this case  
            // we do -not- need to do the full pause cycle (of freezing  
            // and such) because the activity manager assumes it can just  
            // retain the current state it has.  
            try {  
                r.activity.mCalled = false;  
                //同时，由于新activity被调起了，原activity的onPause会被调用  
                mInstrumentation.callActivityOnPause(r.activity);  
                // We need to keep around the original state, in case  
                // we need to be created again.  But we only do this  
                // for pre-Honeycomb apps, which always save their state  
                // when pausing, so we can not have them save their state  
                // when restarting from a paused state.  For HC and later,  
                // we want to (and can) let the state be saved as the normal  
                // part of stopping the activity.  
                if (r.isPreHoneycomb()) {  
                    r.state = oldState;  
                }  
                if (!r.activity.mCalled) {  
                    throw new SuperNotCalledException(  
                        "Activity " + r.intent.getComponent().toShortString() +  
                        " did not call through to super.onPause()");  
                }  
  
            } catch (SuperNotCalledException e) {  
                throw e;  
  
            } catch (Exception e) {  
                if (!mInstrumentation.onException(r.activity, e)) {  
                    throw new RuntimeException(  
                            "Unable to pause activity "  
                            + r.intent.getComponent().toShortString()  
                            + ": " + e.toString(), e);  
                }  
            }  
            r.paused = true;  
        }  
    } else {  
        // If there was an error, for any reason, tell the activity  
        // manager to stop us.  
        try {  
            ActivityManagerNative.getDefault()  
                .finishActivity(r.token, Activity.RESULT_CANCELED, null);  
        } catch (RemoteException ex) {  
            // Ignore  
        }  
    }  
}  


### 7.code：ActivityThread#performLaunchActivity，真正实现Activity的方法
	private Activity performLaunchActivity(ActivityClientRecord r, Intent customIntent) {  
    // System.out.println("##### [" + System.currentTimeMillis() + "] ActivityThread.performLaunchActivity(" + r + ")");  
  
    ActivityInfo aInfo = r.activityInfo;  
    if (r.packageInfo == null) {  
        r.packageInfo = getPackageInfo(aInfo.applicationInfo, r.compatInfo,  
                Context.CONTEXT_INCLUDE_CODE);  
    }  
    //首先从intent中解析出目标activity的启动参数  
    ComponentName component = r.intent.getComponent();  
    if (component == null) {  
        component = r.intent.resolveActivity(  
            mInitialApplication.getPackageManager());  
        r.intent.setComponent(component);  
    }  
  
    if (r.activityInfo.targetActivity != null) {  
        component = new ComponentName(r.activityInfo.packageName,  
                r.activityInfo.targetActivity);  
    }  
  
    Activity activity = null;  
    try {  
        java.lang.ClassLoader cl = r.packageInfo.getClassLoader();  
        //用ClassLoader（类加载器）将目标activity的类通过类名加载进来并调用newInstance来实例化一个对象  
        //其实就是通过Activity的无参构造方法来new一个对象，对象就是在这里new出来的。  
        activity = mInstrumentation.newActivity(  
                cl, component.getClassName(), r.intent);  
        StrictMode.incrementExpectedActivityCount(activity.getClass());  
        r.intent.setExtrasClassLoader(cl);  
        if (r.state != null) {  
            r.state.setClassLoader(cl);  
        }  
    } catch (Exception e) {  
        if (!mInstrumentation.onException(activity, e)) {  
            throw new RuntimeException(  
                "Unable to instantiate activity " + component  
                + ": " + e.toString(), e);  
        }  
    }  
  
    try {  
        Application app = r.packageInfo.makeApplication(false, mInstrumentation);  
  
        if (localLOGV) Slog.v(TAG, "Performing launch of " + r);  
        if (localLOGV) Slog.v(  
                TAG, r + ": app=" + app  
                + ", appName=" + app.getPackageName()  
                + ", pkg=" + r.packageInfo.getPackageName()  
                + ", comp=" + r.intent.getComponent().toShortString()  
                + ", dir=" + r.packageInfo.getAppDir());  
  
        if (activity != null) {  
            Context appContext = createBaseContextForActivity(r, activity);  
            CharSequence title = r.activityInfo.loadLabel(appContext.getPackageManager());  
            Configuration config = new Configuration(mCompatConfiguration);  
            if (DEBUG_CONFIGURATION) Slog.v(TAG, "Launching activity "  
                    + r.activityInfo.name + " with config " + config);  
            activity.attach(appContext, this, getInstrumentation(), r.token,  
                    r.ident, app, r.intent, r.activityInfo, title, r.parent,  
                    r.embeddedID, r.lastNonConfigurationInstances, config);  
  
            if (customIntent != null) {  
                activity.mIntent = customIntent;  
            }  
            r.lastNonConfigurationInstances = null;  
            activity.mStartedActivity = false;  
            int theme = r.activityInfo.getThemeResource()  
            if (theme != 0) {  
                activity.setTheme(theme);  
            }  
  
            activity.mCalled = false;  
            //目标activity的onCreate被调用了，到此为止，Activity被启动了，接下来的流程就是Activity的生命周期了，  
            //本文之前已经提到，其生命周期的各种状态的切换由ApplicationThread内部来完成  
            mInstrumentation.callActivityOnCreate(activity, r.state);  
            if (!activity.mCalled) {  
                throw new SuperNotCalledException(  
                    "Activity " + r.intent.getComponent().toShortString() +  
                    " did not call through to super.onCreate()");  
            }  
            r.activity = activity;  
            r.stopped = true;  
            if (!r.activity.mFinished) {  
                activity.performStart();  
                r.stopped = false;  
            }  
            if (!r.activity.mFinished) {  
                if (r.state != null) {  
                    mInstrumentation.callActivityOnRestoreInstanceState(activity, r.state);  
                }  
            }  
            if (!r.activity.mFinished) {  
                activity.mCalled = false;  
                mInstrumentation.callActivityOnPostCreate(activity, r.state);  
                if (!activity.mCalled) {  
                    throw new SuperNotCalledException(  
                        "Activity " + r.intent.getComponent().toShortString() +  
                        " did not call through to super.onPostCreate()");  
                }  
            }  
        }  
        r.paused = true;  
  
        mActivities.put(r.token, r);  
  
    } catch (SuperNotCalledException e) {  
        throw e;  
  
    } catch (Exception e) {  
        if (!mInstrumentation.onException(activity, e)) {  
            throw new RuntimeException(  
                "Unable to start activity " + component  
                + ": " + e.toString(), e);  
        }  
    }  
  
    return activity;  
	}  


