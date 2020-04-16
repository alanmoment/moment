# Injection Principle Design Pattern

這兩天有個案例，內容簡約來說可以這樣闡述：

### A客戶要簽約買限額的N種廣告，要提供API介接

所以我開始分解上面這段話做設計。

1. 這次A客戶簽約，下次也有機會與B客戶簽約。
2. 廣告有 Header, Body, Footer, Left 及 Right 共5個區域可以放產品，A客戶要買 Header, Body 以及 Right 三種，當然下次別人也有機會買 Footer 或是 Left。
2. A客戶各買 100個, 100個 和 250個產品限額的廣告方案，當然別的客戶也有可能更多亦或更少。
3. A客戶使用的介接格式是 [XML][1]，當然也有機會用到 [Json][2] 或其他種類的資料格式，輸出也是相同。

若是不考慮 Design Pattern 其實這樣子的介接不難做，但要思考的是，假使不做適當的邏輯分離與切割，造成高耦合度，那擴充與修改是否會造成災難。所以我一直覺得寫程式人人都會，但是寫的一手好程式可就不簡單了。而我一直朝著這個目標邁進。

當分析需求完畢，我就開始發想，有哪種設計模式或原則適合這種狀況，應該要避免太複雜的設計模式造成開發困難或結構複雜化。小弟不才，懂得不多。我想到用 Injection Principle 這個原則來做。

## 介接資料

與A客戶協議好用XML資料格式介接，如之前提到的，介接的資料格式是有機會異動的，所以我覺得解析與回傳資料是應該要被分離的。

只要是介接都必須要有解析與回傳，所以我定義介接資料用的介面，裡面必須要實作解析與輸出這兩件事。

	/**
	 * 解析、回傳介接資料介面
	 */
	interface ApiAdParseInterface {
	  
	  /**
	   * 任何解析廣告的資料都必須實作
	   * @param type $content
	   */
	  public function parse($content);
	  
	  /**
	   * 任何回傳廣告的介接結果都必須實作
	   * @param type $content
	   */
	  public function generate($content);
	  
	}

我想到日後或許A客戶也有機會異動介接資料的格式，所以我選擇用 [DI][3] 處理，制訂出配置器加強彈性。

這個配置器主要提供注入的方法，今天若是要實作XML的資料格式，那就在建立這個實體的時候提供 instance。

	/**
	 * 解析、回傳介接結果配置器
	 */
	class ApiParseAdAdapter {
	  
	  private $adapter;
	  
	  public function ApiParseAdAdapter($adapter) {
	    $this->adapter = $adapter;
	  }
	  
	  /**
	   * 解析輸入的資料
	   * @param type $content
	   */
	  public function parse($content) {
	    return $this->adapter->parse($content);
	  }
	  
	  /**
	   * 輸出要產生的資料格式
	   * @param type $content
	   */
	  public function generate($content) {
	    return $this->adapter->generate($content);
	  }
	
	}

實作以 XML 格式介接新增廣告

	/**
	 * 實作解析、生成新增廣告xml資料
	 */
	class ParseAddAdXml implements ApiAdParseInterface {
	
	  public function parse($xml) {
		...
	    return $items;
	  }
	
	  public function generate($content) {
		...
	    return $xml;
	  }
	
	}

以後若是要改用 Json 那就建立 `ParseAddAdJson` 就可以了不用動到其他的程式，相對的安全很多。

## 處理合約

今天是與A客戶簽約，而下次是與B客戶簽約。每份合約各自表述，除了限額與廣告種類，客戶都必須要新增廣告，也都要解析客戶丟過來的資料，以及回傳處理結果。所以我定義了合約的抽象類別，只要是合約都必須要實作解析資料，新增廣告，輸出結果。而為了統一外部的呼叫，則有共用的執行合約方法。

	/**
	 * 統一的合約執行流程
	 */
	abstract class PlanBaseAbstract {
	  
		public function execPlan($xml) {
			$items = $this->parse($xml);
			$content = $this->addAd($items);
			return $this->generate($content);
		}
		
		abstract protected function addAd($items) ;

		abstract protected function parse($content) ;
	
		abstract protected function generate($content) ;
	
	}

再來是處理A客戶的合約內容。

	/**
	 * A客戶的新增廣告內容
	 */
	class ACustomerPlan extends PlanBaseAbstract {
	
		public function addAd($items) 
			...
			在這邊新增各種廣告
			...
		}

		/**
		 * 使用 DI 解析介接資料
		 */
		public function parse($content) {
			$mgr = new ApiParseAdapterMgr(new ParseAddAdXmlMgr());
			$items = $mgr->parse($content);
			...
			return $items;
		}
	
		/**
		 * 使用 DI 輸出介接結果
		 */
		public function generate($content) {
			...
			$mgr = new ApiParseAdapterMgr(new ParseAddAdXmlMgr());
			return $mgr->generate($result);
		}

	}

## 廣告物件

每個廣告都有新增及取得當前數量兩件事，這裡也定義一個介面供各種廣告實作。

	/**
	 * 廣告處理介面
	 */
	interface AdInterface {

		/**
		 * 新增廣告
		 */
		public function add() ;

		/**
		 * 當前廣告數量
		 */
		public function getCount() ;
	
	}

實作廣告的過程中或許有其他的事情要做所以可以寫在各自的廣告物件。這裡就新增一個 Header 當作記錄。

	/**
	 * 實作 Header 廣告
	 */
	class Header implements AdInterface {

		/**
		 * 新增廣告實作
		 */
		public function add() {
			...
		}
	
		/**
		 * 取得當前廣告數量實作
		 */
		public function getCount() {
			...
	    	return (int) $count;
		}
	
	}

未來只要各自實作 Body, Footer 就可以了。這樣也有做到 OCP (開放封閉原則)。

最後用 Class Diagram 解釋這整個關係。

![Class Diagram](https://lh6.googleusercontent.com/-ogfi8AtWedU/Uwb0uIOnJfI/AAAAAAAAAW8/MkVsthE9yVg/w611-h621-no/Class+Diagram.png)

[1]: http://zh.wikipedia.org/zh-tw/XML
[2]: http://www.json.org/
[3]: http://zh.wikipedia.org/zh-tw/%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC

> Feb 26th, 2014 9:33:00pm