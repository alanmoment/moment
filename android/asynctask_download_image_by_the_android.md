# AsyncTask download image by the Android

開發Android時常需要呈現圖片，但不可能圖片都是從Drawable中撈出來，一定有時需要從ImageServer或是透過網址取得圖片。

若是用一般的方式解決

      try {
         URL url = new URL(imageUrl);
         HttpURLConnection connection = (HttpURLConnection) url.openConnection();
         connection.setDoInput(true);
         connection.connect();
         InputStream input = connection.getInputStream();
         Bitmap bitmap = BitmapFactory.decodeStrea (input);
         return bitmap;
      } catch (IOException e) {
         e.printStackTrace();
         return null;
      }

這樣子會造成讀取完圖片才會往下繼續執行的窘境，如果Timeout那畫面會停留更久。所以要用[AsyncTask][]的特性來實作這個功能。

Android 4.0之後為了避免跟網路溝通時造成畫面停滯的問題，在與網路溝通的程式碼中必須要用執行緒的方式來進行與網路的溝通。

## 建立ImageDownloadTask

先在AndroidManifest.xml開啟網路權限

	<uses-permission android:name="android.permission.INTERNET"></uses-permission>

為了避免在短時間內下載重複的圖片，所以用url當作key做了cache的機制`imageCache.put(params[0], mIcon11)`，只要cache中不超過空間限制，都不會重複向遠端撈取圖片。

	package com.example.test;
	
	import android.graphics.Bitmap;
	import android.graphics.BitmapFactory;
	import android.os.AsyncTask;
	import android.support.v4.util.LruCache;
	import android.util.Log;
	import android.widget.ImageView;
	
	import java.io.InputStream;
	import java.net.URLConnection;
	
	/**
	 * 遠端下載圖片
	 *
	 * @author Alan
	 * @since 2013/10/05
	 */
	public class ImageDownloadTask extends AsyncTask<string void bitmap> {
	    private static final String LOG_TAG = ImageDownloadTask.class.getName();
	    private ImageView bmImage;
	    private static LruCache imageCache;
	    private static final int CACHE_SIZE = 1; //1MB
	
	    public ImageDownloadTask(ImageView bmImage) {
	        this.bmImage = bmImage;
	
	        // 設定圖片快取
	        if (imageCache == null) {
	            imageCache = new LruCache(CACHE_SIZE * 1024 * 1024);
	        }
	
	    }
	
	    protected Bitmap doInBackground(String... params) {
	        if (imageCache.get(params[0]) == null) {
	            android.graphics.Bitmap mIcon11 = null;
	            URLConnection urlConnection = null;
	            InputStream in = null;
	            try {
	                urlConnection = new java.net.URL(params[0]).openConnection();
	                urlConnection.setConnectTimeout(3 * 1000);
	                urlConnection.setReadTimeout(10 * 1000);
	                in = urlConnection.getInputStream();
	                mIcon11 = BitmapFactory.decodeStream(in);
	
	                // 記錄已下載圖片的快取
	                imageCache.put(params[0], mIcon11);
	                in.close();
	            } catch (Exception e) {
	                Log.e(LOG_TAG, e.getMessage());
	                e.printStackTrace();
	            }
	
	            return mIcon11;
	        } else {
	            return (Bitmap) imageCache.get(params[0]);
	
	        }
	
	    }
	
	    protected void onPostExecute(android.graphics.Bitmap result) {
	        super.onPreExecute();
	
	        // 設定圖片物件
	        bmImage.setImageBitmap(result);
	        this.bmImage = null;
	    }
	
	}

用起來很簡單

    ImageView iv = new ImageView(this);
    ImageDownloadTask imageDownloadTask = new ImageDownloadTask(iv);
    imageDownloadTask.execute(imageUrl);

[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html

> Oct 18th, 2013 12:22:00am