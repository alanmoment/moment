# Android Universal Image Loader

開發了一陣子的Android，一開始著墨在功能是否能正常運作，現在注重的地方在校能，因為常發生閃退，或未知的錯誤，這邊要講一下下載遠端的圖片加強版。

繼上次發表[AsyncTask Download Image by the Android][1]，這一篇發現了其中有些問題。若是大量的圖片，會產生大量的AsyncTask，會造成閃退，以及其他的AsyncTask無法運作，所以有個工具是[Android-Universal-Image-Loader][2]，它已經幫使用者處理這部份的問題了，使用起來也很簡單。也不會有AsyncTask阻塞的問題。

## Gradle

有兩種方式載入jar檔

1. 官網下載jar檔放進libs，在build.gradle的dependencies中建立`compile files('libs/universal-image-loader-1.8.6.jar')`

2. 自動下載，在build.gradle的dependencies中建立 `compile 'com.nostra13.universalimageloader:universal-image-loader:1.8.6'`

## Usage

我自己寫一隻方便使用的Base
	
	/**
	 * ImageLoader Base
	 *
	 * @author Alan
	 * @since 2013/09/06
	 */
	public class BaseImageLoader {
	
	    private static ImageLoader imageLoader;
	    private static DisplayImageOptions options;
	
	    private String path = "loader_cache";
	    private int stubImage = R.drawable.default;
	    private int emptyImage = R.drawable.default;
	    private int failImage = R.drawable.default;
	    private int fadeInTimestamp = 500;
	
	    public BaseImageLoader(Context context) {
	        File cacheDir = StorageUtils.getOwnCacheDirectory(context, path);
	        imageLoader = com.nostra13.universalimageloader.core.ImageLoader.getInstance();
	        ImageLoaderConfiguration config = new ImageLoaderConfiguration.Builder(context)
	                .discCache(new UnlimitedDiscCache(cacheDir)) // 存在SDcard
	                .discCacheFileNameGenerator(new HashCodeFileNameGenerator())
	                .discCacheExtraOptions(480, 800, Bitmap.CompressFormat.JPEG, 75, null)
	                .discCacheSize(50 * 1024 * 1024) // 快取圖片尺寸
	                .discCacheFileCount(100) // 快取數量
	                .build();
	        imageLoader.init(config);
	    }
	
	    public void setPath(String path) {
	        this.path = path;
	    }
	
	    public void setStubImage(int stubImage) {
	        this.stubImage = stubImage;
	    }
	
	    public void setEmptyImage(int emptyImage) {
	        this.emptyImage = emptyImage;
	    }
	
	    public void setFailImage(int failImage) {
	        this.failImage = failImage;
	    }
	
	    /**
	     * 清除暫存檔
	     */
	    public void clear() {
	        imageLoader.clearDiscCache();
	        imageLoader.clearMemoryCache();
	    }
	
	    public ImageLoader getImageLoader() {
	        return imageLoader;
	    }
	
	    public DisplayImageOptions getOptions() {
	        options = new DisplayImageOptions.Builder()
	                .showStubImage(stubImage) // 載入中顯示的圖片
	                .showImageForEmptyUri(emptyImage) // 找不到連結或是錯誤顯示的圖片
	                .showImageOnFail(failImage) // 圖片解碼錯誤顯示的圖片
	                .imageScaleType(ImageScaleType.EXACTLY) // 圖片縮放方式
	                .cacheInMemory(true)
	                .cacheOnDisc(true)
	//                .displayer(new RoundedBitmapDisplayer(20)) // 載入的圖片加工圓角
	                .displayer(new FadeInBitmapDisplayer(fadeInTimestamp)) // 設置圖片漸顯的時間
	//                .displayer(new SimpleBitmapDisplayer()) // 正常顯示一張圖片
	                .build();
	        return options;
	    }
	}

有了這隻Base的在外面呼叫只需要

	// 建立Base物件
    BaseImageLoader baseImageLoader = new BaseImageLoader(this);

	// 顯示圖片
	ImageView image = (ImageView) convertView.findViewById(R.id.photo);
	baseImageLoader.getImageLoader().displayImage(item.getCover(), image, baseImageLoader.getOptions());

打完收工。用了這個套件，我一次下載幾千張圖片，都不會有閃退的情況了。而且它有很多特性非常方便，諸如圖片圓角、載入中的顯示、找不到圖片的顯示等等，我只列出我需要用到的。

[1]: /post/94171840648/asynctask-download-image-by-the-android
[2]: https://github.com/nostra13/Android-Universal-Image-Loader

> Nov 28th, 2013 4:19:00pm