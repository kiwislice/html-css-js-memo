---
title: 'Entity的繼承'
date: 2019-05-30
weight: 50
tags: ["jpa"]
---





#### @Embeddable

如果只是要重複使用field，並且不介意多一層物件，可使用@Embeddable

```java
@Embeddable
public class RC_OBJ implements Serializable {

	private static final long serialVersionUID = 1L;

	String rcuser;
    String rcdept;
    String rcdate;
    String rctime;
    String rcname;
}
```



```java
@Entity
@Table(name="BCCNTRL")
public class BCCNTRL implements DeptDocument, ExecuteDocument {

	@Embedded
	@AttributeOverrides({ @AttributeOverride(name = "rcuser", column = @Column(name = "RCUSER")),
		@AttributeOverride(name = "rcdept", column = @Column(name = "RCDEPT")),
		@AttributeOverride(name = "rcdate", column = @Column(name = "RCDATE")),
		@AttributeOverride(name = "rctime", column = @Column(name = "RCTIME")),
		@AttributeOverride(name = "rcname", column = @Column(name = "RCNAME")), })
	RC_OBJ rc;
	
}
```



#### @MappedSuperclass

如果只是要重複使用field，並且不介意Entity的extends被用掉，可使用@MappedSuperclass

> 需注意@MappedSuperclass不可與@Entity共用，會報錯：
>
> org.hibernate.AnnotationException: An entity cannot be annotated with both @Entity and @MappedSuperclass

```java
@MappedSuperclass
class HospitalBaseEntity implements Serializable {
	private static final long serialVersionUID = 1L;

	/** 醫院編號(PK) */
	@Id
	@GeneratedValue
	protected int hno;
	/** 醫院代碼(UK) */
	protected String hcode;

}
```



```java
@Entity
@Table(name = "hospital")
public class Hospital extends HospitalBaseEntity {
	private static final long serialVersionUID = 1L;

	public Hospital copy() {
		return ReflectionTool.copyEntity(this);
	}

}
```



#### @Inheritance

尚未實際用過





#### 參考資料

- <https://vladmihalcea.com/the-best-way-to-use-entity-inheritance-with-jpa-and-hibernate/>
- <https://vladmihalcea.com/how-to-inherit-properties-from-a-base-class-entity-using-mappedsuperclass-with-jpa-and-hibernate/>
- <https://thoughts-on-java.org/complete-guide-inheritance-strategies-jpa-hibernate/>

 













