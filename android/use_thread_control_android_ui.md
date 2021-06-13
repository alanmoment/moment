# Use Thread control Android UI

在開發Android app若要用Thread改變UI的話會遇到這個Exception
	
```
android.view.ViewRootImpl$CalledFromWrongThreadException: Only the original thread that created a view hierarchy can touch its views.
at android.view.ViewRootImpl.checkThread(ViewRootImpl.java:4637)
at android.view.ViewRootImpl.invalidateChildInParent(ViewRootImpl.java:876)
at android.view.ViewGroup.invalidateChild(ViewGroup.java:4174)
at android.view.View.invalidate(View.java:10313)
```

原因是Android不允許onCreate之後再建立Thread改變UI，所以得透過他提供的方法

```
// run on ui thread
runOnUiThread(new Runnable() {
    public void run() {
        // TODO
    }
});
```

這樣就可以透過Thread來執行改變UI的事件了。

完整的範例如下，透過Thread 5秒後改變某個UI的屬性

```
// Start thread
@Override
protected void onResume()
{
    super.onResume();
    myThread.start();
}

// Stop thread
@Override
public void onPause()
{
    super.onPause();
    myThread.interrupt();
}

// Execute thread after 5s.
final Thread myThread = new Thread() {
    public void run() {
        try {
            this.sleep(5000);
            // run on ui thread
            runOnUiThread(new Runnable() {
                public void run() {
                    // TODO
                }
            });
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
};
```

> Nov 11th, 2013 11:04:00am
