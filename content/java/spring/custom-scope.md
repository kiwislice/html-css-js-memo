---
title: '自定義Scope'
date: 2019-04-16
weight: 50
---



先準備自定義的Scope物件(此範例為JSF的ViewScope)

```java
import org.springframework.beans.factory.ObjectFactory;
import org.springframework.beans.factory.config.Scope;

public class ViewScope implements Scope {

	public static final String SCOPE_NAME = "custom_view";

    /** 取得給定名稱物件，不存在則建立 */
	@Override
	public Object get(String name, ObjectFactory<?> objectFactory) {
		Map<String, Object> viewMap = FacesContext.getCurrentInstance().getViewRoot().getViewMap();
		if (viewMap.containsKey(name)) {
			return viewMap.get(name);
		} else {
			Object object = objectFactory.getObject();
			viewMap.put(name, object);
			return object;
		}
	}

	@Override
	public String getConversationId() {
		return null;
	}

	@Override
	public void registerDestructionCallback(String name, Runnable callback) {

	}

    /** 移除指定物件 */
	@Override
	public Object remove(String name) {
		return FacesContext.getCurrentInstance().getViewRoot().getViewMap().remove(name);
	}

	@Override
	public Object resolveContextualObject(String arg0) {
		return null;
	}

}
```



準備1個`BeanFactoryPostProcessor`，負責登記Scpoe名稱與對應的Scope物件

```java
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;

public class CustomerBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
	@Override
	public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
		beanFactory.registerScope(ViewScope.SCOPE_NAME, new ViewScope());
	}
}
```



將客製的`BeanFactoryPostProcessor`登記為Bean

```java
@Configuration
public class CustomScopeConfig {
	@Bean
	public static BeanFactoryPostProcessor beanFactoryPostProcessor() {
		return new CustomerBeanFactoryPostProcessor();
	}
}
```



使用方式

```java
@Component
@Scope(ViewScope.SCOPE_NAME)
public class PageView {
}
```



















