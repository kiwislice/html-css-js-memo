---
title: '@OneToMany'
date: 2019-05-30
weight: 50
tags: ["jpa"]
---





##### 簡易範例

```java
@Entity
@Table(name = "staf")
public class Staf implements Serializable {
	private static final long serialVersionUID = 1L;

	/** 職員代碼(PK) */
	@Id
	@GeneratedValue
	private int stafno;
	/** 職員編號 */
	private String empid;
	/** 職員姓名 */
	private String name;
	/** 職位 */
	@OneToMany(mappedBy="stafno", fetch = FetchType.EAGER, cascade=CascadeType.ALL, orphanRemoval=true)
	private Set<Stafpositn> stafpositns;

}
```



#### 注意事項

- 建議使用`fetch=FetchType.EAGER`，如果使用LAZY據說要在`@Transactional`範圍內才不會沒有連線而報錯
- `cascade=CascadeType.ALL, orphanRemoval=true`是為了從`Set`中移除物件並存檔時，可同步移除資料庫內容
- 建議使用`Set`取代`List`，如果使用`@OneToMany List`2個會報`MultipleBagFetchException`，參考[這裡](https://stackoverflow.com/a/5865605)















