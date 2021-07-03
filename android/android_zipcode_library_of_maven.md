# Android zipcode library of maven

最近在做 Android app 時需要用到縣市、區域這些資料，做過網站的，都知道處理這個很煩啊!所以大多都會抽出來另外處理，封裝成 Component 好方便使用，但是在 Android 上要是把縣市、區域、郵遞區號放到 strings.xml 裡面，我想是會瘋掉的，維護、擴充也不容易。我就是建完才後悔的...

![android studio city list](/assets/android/android_zipcode_library_of_maven/android_studio_city_list.PNG)

![android studio area list](/assets/android/android_zipcode_library_of_maven/android_studio_area_list.PNG)

有個不錯的方法就是將資料用 json 的格式存成一隻檔案，然後自己寫抓取 json 的 util 放到 Android 的 libs，這樣要使用就很方便了。

### Eclipse 安裝 Maven

使用 Eclipse plugin 安裝 Maven，在 Install new software 中點選 Add 輸入下列資料。

**Name：Maven Plugin**

**<p>Location：http://download.eclipse.org/technology/m2e/releases</p>**

若是發生不能安裝的錯誤還有另外一個

**Name：Indigo**

**<p>Location：http://download.eclipse.org/releases/indigo</p>**

在這個套件裡尋找 Maven 並且安裝

### 安裝 Maven 工具

[Maven 官網](http://maven.apache.org/download.cgi)下載 Maven 3.1.0 (Binary tar.gz)，放哪裡都可以但是建議不要放在含有空白或特殊字元的資料夾，最後要在環境變數加上以下變數。

**變數名稱：MAVEN_HOME**

**變數值：C:\你的目錄\apache-maven-3.1.0**

在 Path 變數尾端加上 MAVEN_HOME

**%MAVEN_HOME%\bin;**

### 建立 Maven project

為了方便自己管理 library 所以我建立了 maven project

Step 1.

![create maven project step 1](/assets/android/android_zipcode_library_of_maven/create_maven_project.PNG)

Step 2.

![create maven project step 2](/assets/android/android_zipcode_library_of_maven/create_maven_project2.PNG)

Step 3.

![create maven project step 3](/assets/android/android_zipcode_library_of_maven/create_maven_project_step3.PNG)

### 修改 pom.xml

因為要解析 json 格式，所以在這邊需要引入[google-gson](https://code.google.com/p/google-gson/)的 dependency，為什麼不用[json-simple](https://code.google.com/p/json-simple/)呢，因為 Android 並沒有支援這個 library。

在`<dependencies></dependencies>\>`標籤中加入

```xml
<dependency><groupid>com.google.code.gson</groupid><artifactid>gson</artifactid><version>2.2.4</version></dependency>
```

在 project 中 Maven Dependencies 就會增加 gson library

![gson dependencies](/assets/android/android_zipcode_library_of_maven/gson_dependencies.PNG)

### 建立 zipcode.json

依照下圖的格式建立 zipcode.json

![zipcode.json](/assets/android/android_zipcode_library_of_maven/zipcode_json.PNG)

將 zipcode.json 放置在 src/main/resources，並且修改 pom.xml，這樣在使用 maven install 就能夾帶檔案。

在`<project></project>`標籤中加入

```xml
<build><resources><resource><directory>src/main/resources</directory><includes><include>zipcode.json</include></includes></resource></resources></build>
```

### 建立 Zipcode Library

建立 InputStreamReader 讀取 json 資料，再用 gson 解析讀取完畢再關掉。
因為 gson 會主動幫字串加上雙引號，所以取資料出來必須要用 getAsString()，不能用 toString()，否則雙引號不會濾掉。

```java
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
  private JsonObject getJsonObject() {
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
  * @return List<String> citys
  */
  public String[] listCities(){
    JsonArray jsonArray = getJsonObject().getAsJsonArray("city");

    String[] cities = new String[jsonArray.size()];
    int i;
    for (i=0; i<jsonArray.size(); i++) {
      cities[i] = jsonArray.get(i).getAsJsonObject().get("name").getAsString();
    }
    return cities;
  }

  public String getCityName(Integer cityCode) {
    JsonArray jsonArray = getJsonObject().getAsJsonArray("city");
    return jsonArray.get(cityCode).getAsJsonObject().get("name").getAsString();
  }
}
```

> Sep 3rd, 2013 6:50:00pm
