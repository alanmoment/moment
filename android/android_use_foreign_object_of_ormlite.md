# Android use foreign object of OrmLite

Android 也有很好用的 ActiveRecord 可以應用，[OrmLite][]提供了非常多的函式方便應用，架構也非常清楚，在跟 SqlLite 溝通時不再需要自己造輪子。

這邊記錄當 ValueObject 有繼承關係，又必須要有主附表關係時 OrmLite 的 Foreign Key 應用方法。

### 建立 Major

類似像 MySql 的主附表應用，主表如下，`@DatabaseTable`是用在若要另外建立表名，則必須要設定這個值，若沒設定預設會用 class 的名字建立。
`@DatabaseField`則是欄位名稱。

```java
package com.data;

import com.j256.ormlite.field.DatabaseField;
import com.j256.ormlite.table.DatabaseTable;

/**
* Created by Alan on 2013/9/18.
*/
@DatabaseTable(tableName="Major")
public class Major {

  @DatabaseField
  private String parent;

  public void setParent(String parent) {
    this.parent = parent;
  }

  public String getParent() {
    return this.parent;
  }

}
```

### 建立 Minor

當 Minor 與 Major 是繼承關係時，同時又必須要包含另外的物件。這邊的`@DatabaseField`在參數中有`foreign`代表著這個屬性是外部鍵，指向另外一個物件的主鍵。並且`foreignAutoRefresh`是新增一筆資料後不用另外 refresh。

```java
package com.data;

import com.j256.ormlite.field.DatabaseField;
import com.j256.ormlite.table.DatabaseTable;

/**
* Created by Alan on 2013/9/18.
*/
@DatabaseTable(tableName = "Minor")
public class Minor extends Major {

  @DatabaseField(foreign=true, foreignAutoRefresh=true)
  private Other other;

  public Minor() {
  }

  @DatabaseField
  private String child;

  public void setChild(String child) {
    this.child = child;
  }

  public String getChild() {
    return this.child;
  }

  /**
  * 設定外部物件
  */
  public void setOther(Other other) {
    this.other = other;
  }

  /**
  * 取得外部物件
  */
  public Other getOther() {
    return this.other;
  }

}
```

### 建立外部物件

外部物件若要和別的物件有 Composition 的關係，在這邊必須要有主鍵，而我設定`generatedId=true`就類似 MySql 的`auto_increment`屬性，讓物件可以自動生成 ID，以便和別的物件建立指向關係。

```java
package com.data;

import com.j256.ormlite.field.DatabaseField;
import com.j256.ormlite.table.DatabaseTable;

/**
* Created by Alan on 2013/9/18.
*/
@DatabaseTable(tableName="Other")
public class Other {

  @DatabaseField(generatedId=true)
  private Long id;

  @DatabaseField
  private String name;

  public Other() {}

  public void setId(Long id) {
    this.id = id;
  }

  public Long getId() {
    return this.id;
  }

  public void setName(String name) {
    this.name = name;
  }

  public String getName() {
    return this.name;
  }

}
```

### Usage

當這些關係建立後。可以用下列方法取得主物件的屬性

```java
Minor minor = new Minor();
minor.getParent();
```

也可以用下列方法取得外部物件的屬性。

```java
Minor minor = new Minor();
minor.getOther().getName();
```

我覺得用起來超方便的，而且架構很清楚。

[ormlite]: http://ormlite.com/

> Oct 1st, 2013 12:58:00am
