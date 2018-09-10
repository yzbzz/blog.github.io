---
title: Java 编译时注解(RetentionPolicy.CLASS)
date: 2018-04-13 16:07:56
tags: [java, Annotation]
---

### 前言

对于编译时注解`RetentionPolicy.CLASS`，我们需要自定义注解处理器，参照[Java 注解处理器(Annotation Processor)](/2018/04/18/java/java-annotations-processor/)。

### 定义编译时注解

```java
@AutoService(Processor.class)
public class IRouterProcessor extends AbstractProcessor {
	private Messager messager;
	private Elements elementUtils;
	private Filer filer;

	@Override
	public synchronized void init(ProcessingEnvironment processingEnvironment) {
		super.init(processingEnvironment);
		messager = processingEnv.getMessager();
		elementUtils = processingEnv.getElementUtils();
        // 支持通过注解处理器创建新文件
		filer = processingEnv.getFiler();
	}

	@Override
	public Set<String> getSupportedAnnotationTypes() {
		Set<String> types = new LinkedHashSet<>();
		types.add(IRouter.class.getCanonicalName());
		return types;
	}

	@Override
	public SourceVersion getSupportedSourceVersion() {
		return SourceVersion.latestSupported();
	}

	@Override
	public boolean process(Set<? extends TypeElement> annotations,
			RoundEnvironment roundEnv) {
		Set<? extends Element> routerElements = roundEnv.getElementsAnnotatedWith(IRouter.class);
		try {
			if (null != routerElements && !routerElements.isEmpty()) {
				parseRoutes(routerElements);
			}
		} catch (IOException e) {
		}
		return true;
	}

	private void parseRoutes(Set<? extends Element> routerElements)
			throws IOException {
		for (Element element : routerElements) {
			TypeElement typeElement = (TypeElement) element.getEnclosingElement();
			TypeMirror mirror = element.asType();
			ElementKind kind = element.getKind();
			Name name = element.getSimpleName();
		}
	}
}
```

