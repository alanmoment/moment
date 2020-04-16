# Android zipcode library of maven

最近在做Android app時需要用到縣市、區域這些資料，做過網站的，都知道處理這個很煩啊!所以大多都會抽出來另外處理，封裝成Component好方便使用，但是在Android上要是把縣市、區域、郵遞區號放到strings.xml裡面，我想是會瘋掉的，維護、擴充也不容易。我就是建完才後悔的...

![android studio city list](https://lh5.googleusercontent.com/-TmHwJ0cK8AE/UiVUcNB_W4I/AAAAAAAAAI8/rd02S3fJjgM/w388-h244-no/android+studio+city+list.PNG)

![android studio area list](https://lh6.googleusercontent.com/-qZI1udzOqIU/UiVUcMD6JvI/AAAAAAAAAI4/SMF7zw8n2vQ/w357-h196-no/android+studio+area+list.PNG)

有個不錯的方法就是將資料用json的格式存成一隻檔案，然後自己寫抓取json的util放到Android的libs，這樣要使用就很方便了。

### Eclipse安裝Maven

使用Eclipse plugin安裝Maven，在Install new software中點選Add輸入下列資料。

**Name：Maven Plugin**

**<p>Location：http://download.eclipse.org/technology/m2e/releases</p>**

若是發生不能安裝的錯誤還有另外一個

**Name：Indigo**

**<p>Location：http://download.eclipse.org/releases/indigo</p>**

在這個套件裡尋找Maven並且安裝

### 安裝Maven工具

[Maven官網](http://maven.apache.org/download.cgi)下載Maven 3.1.0 (Binary tar.gz)，放哪裡都可以但是建議不要放在含有空白或特殊字元的資料夾，最後要在環境變數加上以下變數。

**變數名稱：MAVEN_HOME**

**變數值：C:\你的目錄\apache-maven-3.1.0**

在Path變數尾端加上MAVEN_HOME

**%MAVEN_HOME%\bin;**

### 建立Maven project

為了方便自己管理library所以我建立了maven project

Step 1.

![create maven project step 1](https://lh4.googleusercontent.com/-DIiAQAtg_L4/UiWSDSUs0wI/AAAAAAAAAJo/bQ3TINy1dBw/w519-h472-no/create+maven+project.PNG)

Step 2.

![create maven project step 2](https://lh6.googleusercontent.com/-oVJkdSqThbE/UiWSDSs_DGI/AAAAAAAAAJY/j0qLEPelwsQ/w614-h268-no/create+maven+project+2.PNG)

Step 3.

![create maven project step 3](https://lh5.googleusercontent.com/-6x01Hr44Rxw/UiW-DTzxcBI/AAAAAAAAAME/ddrbF3yMHHg/w619-h130-no/create+maven+project+step+3.PNG)

### 修改pom.xml

因為要解析json格式，所以在這邊需要引入[google-gson](https://code.google.com/p/google-gson/)的dependency，為什麼不用[json-simple](https://code.google.com/p/json-simple/)呢，因為Android並沒有支援這個library。

在<dependencies></dependencies>\>標籤中加入

	<dependency><groupid>com.google.code.gson</groupid><artifactid>gson</artifactid><version>2.2.4</version></dependency>

在project中Maven Dependencies就會增加gson library

![gson dependencies](https://lh6.googleusercontent.com/-2QPdtz9RYRw/UiWTong0uUI/AAAAAAAAAKA/8zbdFGsia-c/w303-h63-no/gson+dependencies.PNG)

### 建立zipcode.json

依照下圖的格式建立zipcode.json

![zipcode.json](https://lh5.googleusercontent.com/-hv_SKaEiBsc/UiQQShkssYI/AAAAAAAAAH8/r3RUpQjG5Uw/w517-h329-no/zipcode_json.PNG)

將zipcode.json放置在src/main/resources，並且修改pom.xml，這樣在使用maven install就能夾帶檔案。

在<project></project>\>標籤中加入

	<build><resources><resource><directory>src/main/resources</directory><includes><include>zipcode.json</include></includes></resource></resources></build>

### 建立Zipcode Library

建立InputStreamReader讀取json資料，再用gson解析讀取完畢再關掉。
因為gson會主動幫字串加上雙引號，所以取資料出來必須要用getAsString()，不能用toString()，否則雙引號不會濾掉。

	package com.zipcode_util;
	
	import java.io.BufferedReader;
	import java.io.IOException;
	import java.io.InputStreamReader;
	
	import com.google.gson.Gson;
	import com.google.gson.JsonArray;
	import com.google.gson.JsonObject;
	
	public class ZipcodeComponent {
	
		private static final String ZIPCODE_FILE = "zipcode.json";
		private static JsonObject jsonObject;
	
		/**
		 * Json file io
		 * @return JsonObject jsonObject
		 */
		private JsonObject getJsonObject()
		{
			if (jsonObject == null) {
				BufferedReader br = new BufferedReader(
						new InputStreamReader(ZipcodeComponent.class
								.getClassLoader().getResourceAsStream(
										ZipcodeComponent.ZIPCODE_FILE)));
				jsonObject = new Gson().fromJson(br, JsonObject.class);
	
				try {
					br.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
	
			return jsonObject;
		}
	
		/**
		 * Get city name
		 * @return List<string> citys
		 */
		public String[] listCities()
		{
			JsonArray jsonArray = getJsonObject().getAsJsonArray("city");
	
			String[] cities = new String[jsonArray.size()];
			int i;
			for (i=0; i<jsonarray.size i cities jsonarray.get return zipcode util package com.zipcode_util import junit.framework.test junit.framework.testcase junit.framework.testsuite unit test for simple app. public class apptest extends testcase create the case testname name of string super zipcodecomponent system.out.println suite tests being tested static new testsuite apptest.class rigourous :- void testapp asserttrue true tree plugin tool mvn clean install project></jsonarray.size></string>

> Sep 3rd, 2013 6:50:00pm