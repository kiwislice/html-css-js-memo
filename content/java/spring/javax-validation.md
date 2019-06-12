---
title: '使用javax.validation'
date: 2019-06-10
weight: 50
tags: ["spring","hibernate","validation"]
---





目標：在Spring+JSF專案上使用`javax.validation`，用標籤即可達到基本檢查

>  根據<https://beanvalidation.org/2.0/>有實作的包似乎只有[Hibernate Validator](http://hibernate.org/validator/)

```java
public class Sysclitime {
	/** 診別代碼(PK) */
	@Id
	@NotBlank
	private String code;
	/** 語系代碼(PK) */
	@Id
	@NotBlank
	private String langcode;
	/** 診別名稱 */
	@Size(min=0, max=10)
	private String name;
	
}
```



### 遇到的問題

##### 訊息沒有繁體中文

所有訊息都放在`hibernate-validator.jar`的`org.hibernate.validator`中，但就是沒有繁中，因此只好複製修改一份放在`src/main/resource`內的最外層，也就是跟`application.properties`放一起，似乎有設定可以指定位置，但沒有特別研究

##### 客製的訊息

預設訊息內容雖然清楚，但對於表單輸入總是需要說明是哪個欄位有怎樣的問題，因此需要在預設訊息前面加上欄位名稱。因此需要自定`MessageInterpolator`([參考](<https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/?v=6.0#section-custom-message-interpolation>))，但實作起來頗為麻煩，因此改為在驗證完取得`Set<ConstraintViolation<Object>>`後另外處理

##### JSF的自動檢查

由於JSF在validation階段就會自動啟用檢查，是滿方便的，但要改成客製的`Validator`好像有點麻煩，因此可以用`<f:validateBean disabled="true">`把全範圍包起來，就完全關閉JSF自動檢查了

##### Spring與Hibernate沒有共用Validator

預期進行檢查的時間點是在進資料庫之前，也就是Repository的操作，而此時Hibernate也確實會自動進行檢查。由於需要客製訊息，因此需要替換預設的Validator。Spring可以透過設定Bean來替換，但Spring的Validator跟Hibernate沒有共用[*1](<https://github.com/spring-projects/spring-boot/issues/3071>),[*2](<https://github.com/spring-projects/spring-boot/issues/15829>)，所以需要直接替換Hibernate的Validator，考慮所有設定都透過Spring型式，就沒再研究

```java
@Bean
public Validator validator(MessageSource messageSource) {
    LocalValidatorFactoryBean factory = new LocalValidatorFactoryBean();
    factory.setValidationMessageSource(messageSource);
    return factory;
}
```



### 結論

由於需要訊息前方加上欄位名稱，如果無法在`MessageInterpolator`達成，就勢必要手動觸發驗證。要在`MessageInterpolator`達成，就需要替換Hibernate的Validator

















