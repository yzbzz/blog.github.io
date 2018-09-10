---
title: Java 注解处理器(Annotation Processor)
date: 2018-04-18 18:48:52
tags: [java, Annotation]
---

### 前言

注解处理器(Annotation Processor Tool)是`javac`的一个工具，主要用于在编译时处理`编译时注解RetentionPolicy.CLASS）`。我们可以自定义注解处理器，获取我们自定义的注解信息，通过注解信息生成`java`文件(如： [ButterKnife](https://github.com/JakeWharton/butterknife))或进行其它的一些操作

### 自定义注解处理器

自定义注解处理器需要继承于`AbstractProcessor`，一般我们会重写下面4个方法

```java
public class IRouterProcessor extends AbstractProcessor {
	
	private Messager messager; // Log 日志
	private Elements elementUtils; // 操作Element工具类
	private Filer filer; // 支持通过注解处理器创建新文件
	private Map<String, String> options; // 额外配置参数

	@Override
	public synchronized void init(ProcessingEnvironment processingEnvironment) {
		super.init(processingEnvironment);
		messager = processingEnvironment.getMessager();
		elementUtils = processingEnvironment.getElementUtils();
		filer = processingEnvironment.getFiler();
		options = processingEnvironment.getOptions();
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

- init(ProcessingEnvironment env):  每一个注解处理器类都必须有一个空的构造函数。然而，这里有一个特殊的init()方法，它会被注解处理工具调用，并输入ProcessingEnviroment参数。ProcessingEnviroment提供很多有用的工具类Elements, Types和Filer。后面我们将看到详细的内容。
- process(Set<? extends TypeElement> annotations, RoundEnvironment env) :  这相当于每个处理器的主函数main()。你在这里写你的扫描、评估和处理注解的代码，以及生成Java文件。输入参数RoundEnviroment，可以让你查询出包含特定注解的被注解元素。后面我们将看到详细的内容。
- getSupportedAnnotationTypes():  这里你必须指定，这个注解处理器是注册给哪个注解的。注意，它的返回值是一个字符串的集合，包含本处理器想要处理的注解类型的合法全称。换句话说，你在这里定义你的注解处理器需要处理哪些注解。
- getSupportedSourceVersion(): 用来指定你使用的Java版本。通常这里返回`SourceVersion.latestSupported()`。然而，如果你有足够的理由只支持Java 6的话，你也可以返回SourceVersion.RELEASE_6。我推荐你使用前者。

在Java 7中，你也可以使用注解来代替getSupportedAnnotationTypes()和getSupportedSourceVersion()，像这样：

```java
@SupportedSourceVersion(SourceVersion.latestSupported())
@SupportedAnnotationTypes({
   // 合法注解全名的集合
 })
public class IRouterProcessor extends AbstractProcessor {

    @Override
    public synchronized void init(ProcessingEnvironment env){ }

    @Override
    public boolean process(Set<? extends TypeElement> annoations, RoundEnvironment env) {}
}
```

因为兼容的原因，特别是针对Android平台，我建议使用重载`getSupportedAnnotationTypes()`和`getSupportedSourceVersion()`方法代替`@SupportedAnnotationTypes`和

`@SuppozrtedSourceVersion`

接下来的你必须知道的事情是，注解处理器是运行它自己的虚拟机JVM中。是的，你没有看错，**javac**启动一个完整Java虚拟机来运行注解处理器。这对你意味着什么？你可以使用任何你在其他java应用中使用的的东西。使用guava。如果你愿意，你可以使用依赖注入工具，例如dagger或者其他你想要的类库。但是不要忘记，即使是一个很小的处理，你也要像其他Java应用一样，注意算法效率，以及设计模式。

**注册你的处理器**

你可能会问，我怎样将处理器IRouterProcessor注册到javac中。你必须提供一个**.jar**文件。就像其他**.jar**文件一样，你打包你的注解处理器到此文件中。并且，在你的jar中，你需要打包一个特定的文件javax.annotation.processing.Processor到META-INF/services路径下。所以，你的.jar文件看起来就像下面这样：

- IRouterProcessor.jar
- -- com
- -- -- example
- -- -- -- IRouterProcessor.class
- -- META-INF
- -- -- services
- -- -- -- javax.annotation.processing.Processor

打包进IRouterProcessor.jar中的javax.annotation.processing.Processor的内容是，注解处理器的合法的全名列表，每一个元素换行分割：

```java
com.example.IRouterProcessor  
com.foo.OtherProcessor  
net.blabla.SpecialProcessor  
```

把IRouterProcessor.jar放到你的builpath中，**javac**会自动检查和读取javax.annotation.processing.Processor中的内容，并且注册IRouterProcessor作为注解处理器。

**使用@AutoService注册你的处理器**

如果你觉得上面一步`注册你的处理器`比较麻烦的话，可以下载google的`auto-server`包

```
implementation 'com.google.auto.service:auto-service:1.0-rc4'
```

在你的注解器上加上`@AutoService(Processor.class)`，这样你就可以忽略上一步的`注册你的处理器`章节，以下是完整的类

```java
@AutoService(Processor.class)
public class IRouterProcessor extends AbstractProcessor {
	
	private Messager messager; // Log 日志
	private Elements elementUtils; // 操作Element工具类
	private Filer filer; // 支持通过注解处理器创建新文件
	private Map<String, String> options; // 额外配置参数

	@Override
	public synchronized void init(ProcessingEnvironment processingEnvironment) {
		super.init(processingEnvironment);
		messager = processingEnvironment.getMessager();
		elementUtils = processingEnvironment.getElementUtils();
		filer = processingEnvironment.getFiler();
		options = processingEnvironment.getOptions();
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

这样，编译时就会执行`process`方法，在这个方法里，你可以写自己的逻辑，生成java文件等等