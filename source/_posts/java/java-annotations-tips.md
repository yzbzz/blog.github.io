---
title: Java注解知识点
date: 2018-09-07 16:52:12
tags: [java, Annotation]
---

### ProcessingEnvironment

源码

```java
public interface ProcessingEnvironment {
    Map<String,String> getOptions();
    Messager getMessager();
    Filer getFiler();
    Elements getElementUtils();
    Types getTypeUtils();
    SourceVersion getSourceVersion();
    Locale getLocale();
}
```

主要方法

| 方法名            | 返回值                                    | 描述                                                   |
| ----------------- | ----------------------------------------- | ------------------------------------------------------ |
| getOptions()      | <a href="#Options">Map<String,String></a> | 返回指定的参数选项                                     |
| getMessager()     | <a href="#Messager">Messager</a>          | 返回实现Messager接口的对象，用于报告错误信息、警告提醒 |
| getFiler()        | <a href="#Filer">Filer</a>                | 返回实现Filer接口的对象，用于创建文件、类和辅助文件    |
| getElementUtils() | <a href="#Element">Elements</a>           | 返回实现Elements接口的对象，用于操作元素的工具类       |
| getTypeUtils()    | Types                                     | 返回实现Types接口的对象，用于操作类型的工具类          |

通过getOptions()方法获取选项参数，在gradle文件中配置选项参数值

```groovy
android {
    defaultConfig {
        javaCompileOptions {
            annotationProcessorOptions {
                arguments = [ version : '1.0.0' ]
            }
        }
    }
}
```

通过`ProcessingEnvironment`去获取对应的参数

```
processingEnvironment.getOptions().get("version");
```

### RoundEnvironment

源码：

```java
public interface RoundEnvironment {
  
    boolean processingOver();

    boolean errorRaised();

    Set<? extends Element> getRootElements();

    Set<? extends Element> getElementsAnnotatedWith(TypeElement a);

    Set<? extends Element> getElementsAnnotatedWith(Class<? extends Annotation> a);
}
```

主要方法

| 方法名                                                  | 返回值                 | 描述                                                      |
| ------------------------------------------------------- | ---------------------- | --------------------------------------------------------- |
| getElementsAnnotatedWith(Class<? extends Annotation> a) | Set<? extends Element> | 通给给定的注解，获取Element元素列表（获取注解标示的元素） |

### Element

源码：

```java
public interface Element extends javax.lang.model.AnnotatedConstruct {
    TypeMirror asType();

    ElementKind getKind();

    Set<Modifier> getModifiers();

    Name getSimpleName();

    Element getEnclosingElement();

    List<? extends Element> getEnclosedElements();
    
    @Override
    boolean equals(Object obj);
    
    @Override
    int hashCode();
    
    @Override
    List<? extends AnnotationMirror> getAnnotationMirrors();
    
    @Override
    <A extends Annotation> A getAnnotation(Class<A> annotationType);
    
    <R, P> R accept(ElementVisitor<R, P> v, P p);
}
```

主要子类

| 类型                                                     | 描述                                                         |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| <a href="#PackageElement">PackageElement</a>             | 包元素。提供对有关包及其成员的信息的访问                     |
| <a href="#ExecutableElement">ExecutableElement</a>       | 类、接口的方法元素。包括构造方法、注解类型                   |
| <a href="#VariableElement">VariableElement</a>           | 字段、enum、方法、构造方法参数、局部变量、资源变量、异常参数 |
| <a href="#TypeElement">TypeElement</a>                   | 类、接口元素。提供对有关类型及其成员的信息的访问             |
| <a href="#TypeParameterElement">TypeParameterElement</a> | 类、接口、方法、构造方法的参数元素                           |

主要方法

| 方法名                | 返回值                                 | 描述                                                   |
| --------------------- | -------------------------------------- | ------------------------------------------------------ |
| getKind()             | <a href="#ElementKind">ElementKind</a> |                                                        |
| asType()              | <a href="#TypeMirror">TypeMirror</a>   | 返回元素的数据类型（基本类型、引用类型、数组类型等等） |
| getSimpleName()       | Name                                   |                                                        |
| getEnclosingElement() | Element                                |                                                        |

### ElementKind

元素的类型，元素的类型判断不需要用`instanceof`去判断，而应该通过`Element.getKind()`去判断对应的类型

源码：

```java
public enum ElementKind {
    /** A package. */
    PACKAGE,

    /** An enum type. */
    ENUM,
    /** A class not described by a more specific kind (like {@code ENUM}). */
    CLASS,
    /** An annotation type. */
    ANNOTATION_TYPE,
    
    INTERFACE,

    ENUM_CONSTANT,

    FIELD,
    /** A parameter of a method or constructor. */
    PARAMETER,
    /** A local variable. */
    LOCAL_VARIABLE,
    /** A parameter of an exception handler. */
    EXCEPTION_PARAMETER,

    // Executables
    /** A method. */
    METHOD,
    /** A constructor. */
    CONSTRUCTOR,
    /** A static initializer. */
    STATIC_INIT,
    /** An instance initializer. */
    INSTANCE_INIT,

    TYPE_PARAMETER,

    OTHER,

    RESOURCE_VARIABLE;

    public boolean isClass() {
        return this == CLASS || this == ENUM;
    }

    public boolean isInterface() {
        return this == INTERFACE || this == ANNOTATION_TYPE;
    }

    public boolean isField() {
        return this == FIELD || this == ENUM_CONSTANT;
    }
}
```

主要类型

| 属性            | 描述     |
| --------------- | -------- |
| PACKAGE         | 包       |
| ENUM            | 枚举     |
| CLASS           | 类       |
| ANNOTATION_TYPE | 注解     |
| INTERFACE       | 接口     |
| ENUM_CONSTANT   | 枚举常量 |
| FIELD           | 字段     |
| PARAMETER       | 方法参数 |
| LOCAL_VAR       | 局部变量 |
| METHOD          | 方法     |
| CONSTRUCTOR     | 构造方法 |
| TYPE_PARAMETER  | 类型参数 |

### TypeMirror

元素的数据类型，基本类型、引用类型、数组类型等等

源码：

```java
public interface TypeMirror extends javax.lang.model.AnnotatedConstruct {

    TypeKind getKind();

    boolean equals(Object obj);

    int hashCode();

    String toString();

    <R, P> R accept(TypeVisitor<R, P> v, P p);
}
```

主要子类

| 类型             | 描述     |
| ---------------- | -------- |
| IntersectionType | - |
| UnionType        | - |
| ExecutableType   | Executable类型(方法、构造方法、初始化) |
| NoType           | 在实际类型不适合的地方使用的伪类型 |
| PrimitiveType    | 基本数据类型 |
| WildcardType     | 通配符类型参数 |
| ReferenceType    | 引用类型 |
| NullType    | Null类型 |
| DeclaredType    | 声明类型(类或接口类型) |
| ErrorType    | 异常类或接口类型) |
| TypeVariable    | 类型变量 |
| ClassType        | 类类型 |
| ArrayType        | 数组类型 |
| PackageType      | 包类型 |
| AnnotatedType    | 注解类型 |
| MethodType       | 方式类型 |

主要方法

| 方法    | 返回类型                           | 描述           |
| ------- | ---------------------------------- | -------------- |
| getKind | <a href="#TypeMirror">TypeKind</a> | 返回类型的属性 |

### TypeKind

类型的属性，类型的属性判断不需要用`instanceof`去判断，而应该通过`TypeMirror.getKind()`去判断对应的属性

源码：

```java
public enum TypeKind {
    BOOLEAN,

    BYTE,

    SHORT,

    INT,

    LONG,

    CHAR,

    FLOAT,
    
    DOUBLE,

    VOID,

    NONE,

    NULL,

    ARRAY,
    
    DECLARED,

    ERROR,

    TYPEVAR,

    WILDCARD,

    PACKAGE,

    EXECUTABLE,

    OTHER,

    UNION,

    INTERSECTION;

    public boolean isPrimitive() {
        switch(this) {
        case BOOLEAN:
        case BYTE:
        case SHORT:
        case INT:
        case LONG:
        case CHAR:
        case FLOAT:
        case DOUBLE:
            return true;

        default:
            return false;
        }
    }
}
```

主要属性

| 属性       | 描述                     |
| ---------- | ------------------------ |
| BOOLEAN    | 基本类型booelan          |
| INT        | 基本类型int              |
| LONG       | 基本类型long             |
| FLOAT      | 基本类型float            |
| DOUBLE     | 基本类型double           |
| VOID       | 对应于关键字void的伪类型 |
| NULL       | null类型                 |
| ARRAY      | 数组类型                 |
| PACKAGE    | 对应于包元素的伪类型     |
| EXECUTABLE | 方法、构造方法、初始化   |

这里需要注意的是，如果我们通过注解去获取Class类型的值，如果获取的Class未被编译，则会抛出MirroredTypeException异常，此时我们需要通过try-catch语句在catch里去获取我们所需要的类元素

```java
try {  
    annotation.value();//如果value为Class类型则会报异常
} catch (MirroredTypeException mte) {
    DeclaredType classTypeMirror = (DeclaredType) mte.getTypeMirror();
    TypeElement classTypeElement = (TypeElement) classTypeMirror.asElement();//通过异常去获取类元素
}
```

### Filer

Filer接口支持通过注解处理器创建新文件。可以创建三种文件类型：源文件、类文件和辅助资源文件

源码：

```java
public interface Filer {
   
    JavaFileObject createSourceFile(CharSequence name,
                                    Element... originatingElements) throws IOException;

   
    JavaFileObject createClassFile(CharSequence name,
                                   Element... originatingElements) throws IOException;

    FileObject createResource(JavaFileManager.Location location,
                             CharSequence pkg,
                             CharSequence relativeName,
                             Element... originatingElements) throws IOException;

    FileObject getResource(JavaFileManager.Location location,
                           CharSequence pkg,
                           CharSequence relativeName) throws IOException;
}
```

| 方法             | 返回值         | 描述             |
| ---------------- | -------------- | ---------------- |
| createSourceFile | JavaFileObject | 创建源文件       |
| createClassFile  | JavaFileObject | 创建类文件       |
| createResource   | FileObject     | 创建辅助资源文件 |

### Messager

Messager接口提供注解处理器用来报告错误消息、警告和其他通知的方式

源码：

```java
public interface Messager {

    void printMessage(Diagnostic.Kind kind, CharSequence msg);

    void printMessage(Diagnostic.Kind kind, CharSequence msg, Element e);

    void printMessage(Diagnostic.Kind kind, CharSequence msg, Element e, AnnotationMirror a);
    
    void printMessage(Diagnostic.Kind kind,
                      CharSequence msg,
                      Element e,
                      AnnotationMirror a,
                      AnnotationValue v);
}
```

| 方法名       | 描述         |
| ------------ | ------------ |
| printMessage | 打印错误消息 |

> 参考：
>
> https://blog.csdn.net/qq_30379689/article/details/82345625

