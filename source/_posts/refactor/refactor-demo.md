---
title: refactor-demo
date: 2017-03-21 11:56:09
tags: read
---

### 重新组织函数

##### Extract Method (提炼函数)
(将这段代码放进一个独立函数中，并让函数名称解释该函数的用途)
```java
void printOwing(double amount) {
  printBanner();
  
  // print details
  System.out.println("name: " + _name);
  System.out.println("amout: " + amount);
}
==>
void printOwing(double amount) {
  printBanner();
  printDetails(amount);
}

void  printDetails(double amount) {
  System.out.println("name: " + _name);
  System.out.println("amout: " + amount);
}


```

<!--more-->

##### Inline Method (内联函数)

（在函数调用点插入函数体，然后移除该函数）

```java
int getRating() {
  return (moreThanFiveLateDeliveries()) ? 2 : 1;
}
boolean moreThanFiveLateDeliveries() {
  return _numberOfLateDeliveries > 5;
}
==>
int getRating() {
  return (_numberOfLateDeliveries > 5) ? 2 : 1;
} 
  
```

##### Inline Temp (内联临时变量)

（将所有对该变量的引用动作，替换为对它赋值的那个表达式自身。）

```java
double basePrice = anOrder.basePrice();
return (basePrice > 1000);
==>
return (anOrder.basePrice() > 1000);
```

##### Replace Temp with Query（以查询取代临时变量）

(将这个表达式提炼到一个独立函数中，将这个临时变量的所有引用点替换为对新函数的调用。此后，新函数就可被其他函数使用。)

```java
double basePrice = _quantity * _itemPrice;
if (basePrice > 1000) {
  return basePrice * 0.95;
} else {
  return basePrice * 0.98;
}
==>
if (basePrice() > 1000) {
  return basePrice() * 0.95;
} else {
  return basePrice * 0.98;
}
double basePrice() {
  return _quantity * _itemPrice;
}
```

##### Introduce Explaining Variable（引用解释性变量）

(将该复杂表达或其中一部分的结果放进一个临时变量，以此变量名称来解释表达式的用途。)

```java
if ((platform.toUpperCase().indexOf("MAC") > -1) &&
    (browser.toUpperCase().indexOf("IE") > -1) &&
    wasInitialized() && resize > 0 {
      
   // do something
}
==>
final boolean isMacOs = platform.toUpperCase().indexOf("MAC") > -1;
final boolean isIEBrowser = browser.toUpperCase().indexOf("IE") > -1;
final boolean wasResized = resize > 0;

if (isMacOs && isIEBrowser && wasInitialized() && wasResized) {
  // do something
}
```

##### Split Temporary Variable（分解临时变量）

（针对每次赋值，创造一个独立、对应的临时变量）

```java
double temp = 2 * (_height + _width);
System.out.println(temp);
temp = _height * _width;
System.out.println(temp);
==>
final double perimeter = 2 * (_height + _width);
System.out.println(perimeter);
final double area = _height * _width;
System.out.println(area);
```

##### Remove Assignments to Parameters

(以一个临时变量取代该参数的位置)

```java
int discount(int inputVal, int quantity, int yearToDate) {
  if (inputVal > 50) {
    inputVal -= 2;
  }
}
==>
int discount(int inputVal, int quantity, int yearToDate) {
  int result = inputVal;
  if (inputVal > 50) {
    result -= 2;
  }
} 
```

##### Replace Method with Method Object(以函数对象取代函数)

(将这个函数放进一个单独对象中，如此一来局部变量就成了对象内的字段。然后你可以在同一个对象中将这个大型函数分解为多个小型函数)

```java
class Order {
  double price() {
    double primaryBasePrice
    double secondaryBasePrice
    ....
    return primaryBasePrice + secondaryBasePrice + ...
  }
}
==>
class Order {
  double price() {
    return new PriceCalculator(this).compute();
  }
}
class PriceCalculator {
  double primaryBasePrice
  double secondaryBasePrice
  ...
  double compute() {
    return primaryBasePrice + secondaryBasePrice + ...
  }
}
```

##### Substitute Algorithm（替换算法）

（将函数本体替换为另一个算法）

```java
String foundPerson(String[] people) {
  for (int i = 0; i < people.length;i++) {
    if (people[i].equals("Don")) {
      return "Don";
    }
    ...
  }
  return "";
}
==>
String foundPerson(String[] people) {
  List candidates = Arrays.asList(new String[] {"Don","John","Kent"});
  for (int i = 0; i < people.length;i++) {
    if (candidates.contains(people[i])) {
      return people[i];
    }
  }
  return "";
}  
```



### 在对象之间搬移特性

##### Move Method （搬移函数）

"搬移函数"是重构理论的支柱。如是一个类有太多行为，或如果一个类与另一个类有太多合作而形成高度耦合，我就会搬移函数。通过这种手段，可以使系统中的类更简单，这些类最终也将更干净利落地实现系统交付的任务。

##### Move File (搬移字段)

在类之间移动状态和行为，是重构过程中必不可少的措施。随着系统的发展，你会发现自己需要新的类，并需要将现有的工作责任拖到新的类中。在这个星期看似合理而正确的设计决策，到了下个星期可能不再正确。这没问题。如果你从来没遇到这种情况，那才有问题。

##### Extract Class（提炼类）

（建立一个新类，将相关的字段和函数从旧类搬移到新类）

```java
class Person {
  String name, officeAreaCode, officeNumber;
  void getTelephoneNumber(){}
}
==>
class Person {
  String name;
  void getTelephoneNumber(){}
}
class TelephoneNumber {
  String areaCode, number;
  void getTelephoneNumber(){}
}
```

##### Inline Class（将类内联化）

将这个类的所有特性搬移到别一个类中，然后移除原类。

##### Hide Delegate（隐藏"委托关系"）

在服务类上建立客户所需的所有函数，用以隐藏委托关系。

##### Remove Middle Man（移除中间人）

让客户直接调用受托类

##### Introduce Foreign Method（引入外加函数）

（在客户类中建立一个函数，并以第一参数形式传入一个服务类实例）

```java
Date newStart = new Date(previousEnd.getYear(), previousEnd.getMonth(), previousEnd.getDate() + 1);
==>
Date newStart = nextDay(previousEnd);
private static Date nextDay(Date arg) {
  return new Date(previousEnd.getYear(), previousEnd.getMonth(), previousEnd.getDate());
}

```

##### Introduce Local Extension（引入本地扩展）

建立一个新类，使它包含这些额外函数。让这个扩展品成为源类的子类或包装类。



### 重新组织数据

##### Self Encapsulate Field（自封装字段）

（为这个字段建立取值/设值函数，并且只以这些函数来访问字段）

```java
private int _low, _hight;
boolean includes (int arg) {
  return arg >= _low && arg <= _high;
}
==>
private int _low, _hight;
boolean includes (int arg) {
  return arg >= getLow() && arg <= getHigh();
}
int getLow() {return _low;}
int getHight() {return _hight;}
```

##### Replace Data Value with Object（以对象取代数据值）

你有一个数据，需要与其他数据和行为一起使用才有意义。

##### Change Value to Reference（将值对象改为引用对象）

你从一个类衍生出许多彼此相等的实例，希望将它们替换为同一个对象。**将这个值对象变世引用对象**

##### Change Reference to Value （将引用对象改为值对象）

你有一个引用对象，很小且不可变，而且不易管理。**将它变成一个值对象**。

##### Replace Array with Object（以对象取代数组）

你有一个数组，其中的元素各自代表不同的东西。

(以对象替换数组，对于数组中的每个元素，以一个字段来表示)

```java
String[] row = new String[3];
row[0] = "Liverpool";
row[1] = "15";
==>
Performance row = new Performance();
row.setName("Liverpool");
row.setWins("15");
```

##### Duplicate Observed Data（复制"被监视数据"）

你有一些领域数据置身于GUI控件中，而领域函数需要访问这些数据。（将该数据复制到一个领域对象中。建立一个Observer模式，用以同步领域对象和GUI对象内的重复数据）

##### Change Unidirectional Association to Bidirectional（将单向关联改为双向关联）

两个类都需要使用对方特性，但其间只有一条单向连接。（添加一个返回指针，并使修改函数能够同时更新两条连接）

**Change Bidirectional Association to Unidirectional （将双向关联改为单向关联）**

两个类之间有双向关联，但其中一个类如今不再需要另一个类的特性。（去除不必要的关联）

**Replace Magic Number with Symbolic Constant（以字面常量取代魔法数）**

（创建一个常量，根据其意义为它命名，并将上述的字面数值替换为这个常量。）

```java
double potentialEnergy(double mass, double height) {
  return mass * 9.81 * height;
}
==>
double potentialEnergy(double mass, double height) {
  return mass * GRAVITATIONAL_CONSTANT * height;
}
static final double GRAVITATIONAL_CONSTANT = 9.81;
```

**Encapsulate Field（封装字段 ）**

你的类中存在一个public字段。将它声明为private，并提供相应的访问函数。

```java
public String _name;
==>
private String _name;
public String getName() {return _name;}
public void setName(String arg) {_name = arg;}
```

##### Encapsulate Collection（封装集合）

有个函数返回一个集合。（让这个函数返回该集合的一个只读副本，并在这个类中提供添加、移除集合元素的函数。）

##### Replace Recore with Data Class（以数据类取代记录）

你需要面对传统编程环境中的记录结构。为该记录创建一个"哑"数据对象。

##### Replace Type Code with Class（以类取代类型码）

类之中有一个数值类型码，但它并不影响类的行为。（以一个新的类替换该数值类型码。）

```java
class Person {
  int O, A, B, AB;
  int bloodGroup;
}
==>
class Person {}
class BloodGroup {
  BloodGroup O, A, B, AB
}
```

##### Replace Type Code with Subclasses（以子类取代类型码）

你有一个不可变的类型码，它会影响类的行为：

```java
class Employee {
  int ENGINEER;
  int SALESMAN;
}
==>
class Employee {}
class Engineer {}
class Salesman {}
```

##### Replace Type Code with State/Strategy（以State/Strategy取代类型码）

你有一个类型码，它会影响类的行为，但你无法通过继承手法消除它。（以状态对象取代类型码）

##### Replace Subclass with Fields（以字段取代子类）

你的各个子类的唯一差别只在"返回常量数据"的函数身上。修改这些函数，使它们返回超类中的某个（新增）字段，然后销毁子类



### 简化条件表达式

##### Decompose Conditional（分解条件表达式）

（从if、then、else三个段落中分别提炼出独立函数）

```java
if (date.before (SUMMER_START) || date.after(SUMMER_END)) {
  charge = quantity * _winterRate + _winterServiceCharge;
} else {
  charge = quantity * _summberRate;
}
==>
if (notSummer(date)) {
  charge = winterCharge(quantity);
} else {
  charge = summerCharge(quantity);
}
```

##### Consolidate Conditional Expression（合并条件表达式）

你有一系列条件测试，都得到相同结果。（将这些测试合并为一个条件表达式，并将这个条件表达式提炼成为一个独立函数）

```java
double disabilityAmount () {
  if (_seniority < 2) { return 0;}
  if (_monthsDisabled > 12) { return 0;}
  if (_isPartTime) { return 0;}
}
==>
double disabilityAmount() {
  if (isNotEligibleForDisability()) {return 0;}
}
```

##### Consolidate Duplicate Conditional Fragments（合并重复的条件片段）

将下面这段重复代码搬移到条件表达式之外。

```java
if (isSpecialDeal()) {
  total = price * 0.95;
  send();
} else {
  total = price * 0.98;
  send();
}
==>
if (isSpecialDeal()) {
  total = price * 0.95;
} else {
  total = price * 0.98;
}
send();
```
##### Remove Control Flag（移除控制标记）

以break语句或return语句取代控制标记

##### Replace Nested Conditional with Guard Clauses（以卫语句取代嵌套条件表达式）

函数中的条件逻辑使人难以看清正常的执行路径。（使用卫语句表现所有特殊情况）

```java
double getPayAmount() {
  double result;
  if (_isDead) {
    result = deadAmount();
  } else {
    if (_isSeparated) {
      result = separatedAmount();
    } else {
      if (_isRetired) {
        result = retiredAmount();
      } else {
        result = normalPayAmount();
      } 
    }
  }
}
==>
double getPayAmount() {
  if (_isDead) {
    return deadAmount();
  }
  if (_isSeparated) {
    return separatedAmount();
  }
  if (_isRetired) {
    return retiredAmount();
  } 
  return normalPayAmount();
}
```

##### Replace Conditional with Polymorphism（以多态取代条件表达式）

你手上有个条件表达式，它根据对象类型的不同而选择不同的行为。（将这个条件表达式的每个分支放进一个子类内的覆写函数中，然后将原始函数声明为抽象函数。）

```java
double getSpeed() {
  switch(_type) {
    case EUROPEAN:
      return getBaseSpeed;
    case AFRICAN:
      return getBaseSpeed() - getLoadFactor() * _numberOfCoconuts;
    case NORWEGINA_BLUE:
      return (_isNailed) ? 0 : getBaseSpeed(_voltage);
  }
  throw new RuntimeException("Should be unreachable");
}
==>
abstract class Bird { abstract getSpeed()}
class European extends Bird { getSpeed()}
class African extends Bird { getSpeed()}
class NorwegianBlue extends Bird { getSpeed()}
```

##### Introduce Null Object（引用Null对象）

你需要再三检查某个对象是否为null。（将null值替换为null对象）

```java
if (customer == null) {
  plan = BillingPlan.basic();
} else {
  plan = customer.getPlan();
}
==>
class Customer { getPlan()}
class NullCustomer extends Customer {getPlan()}
```

##### Introduce Assertion（引入断言）

某一段代码需要对程序状态做出某种假设。以断言明确表现这种假设。

```java
double getExpenseLimit() {
  return (_expenseLimit != NULL_EXPENSE) ? _expenseLimit : _primaryProject.getMemberExpenseLimit();
}
==>
double getExpenseLimit() {
  Assert.isTrue (_expenseLimit != NULL_EXPENSE || _primaryProject != null);
  return (_expenseLimit != NULL_EXPENSE) ? _expenseLimit : _primaryProject.getMemberExpenseLimit();
}
```



### 简化函数调用

##### Rename Method（函数改名）

函数的名称未能指示函数的用途。修改函数名称。

```java
class Customer {getinvcdtlmt()}
==>
class Customer {getInvoiceableCreditLimit()}
```

##### Add Parameter（添加函数）

某个函数需要从调用端得到更多信息。（为些函数添加一个对象参数，让该对象带进函数所需的信息）

##### Remove Parameter（移除参数）

函数本体不再需要某个参数。将该参数去除。

##### Separate Query from Modifier（将查询函数和修改函数分离）

某个函数既返回对象状态值，又修改对象状态。（建立两个不同的函数，其中一个负责查询，别一个负责修改。）

```java
class Customer {getTotalOutstandingAndSetReadyForSummaries()}
==>
class Customer {
  getTotalOutstanding()
  setReadyForSummaries()
}
```

##### Parameterize Method（令函数携带参数）

若干函数做了类似的工作，但在函数本体中却包含了不同的值。（建立单一函数，以参数表达那些不同的值。）

```java
class Employee { 
  fivePercentRaise();
  tenPercentRaise();
}
==>
class Employee {
  raise(percentage);
}
```

##### Replace Parameter with Explicit Methods（以明确函数取代参数）

你有一个函数，其中完全取决于参数值而采取不同行为。（针对该参数的每一个可能值，建立一个独立函数。）

```java
void setValue(String name, int value) {
  if (name.equals("height")) {
    _height = value;
    return;
  }
  if (name.equals("width")) {
    _width = value;
    return;
  }
  Assert.shouldNeverReachHere();
}
==>
void setHeight(int arg) {
  _height = arg;
}
void setWidth(int arg) {
  _width = arg;
}
```

##### Preserve Whole Object（保持对象完整）

你从某个对象中取出若干值，将它们作为某一次函数调用的参数。改为传递整个对象。

```java
int low = daysTempRange().getLow();
int hight = daysTempRange().getHeight;
withinPlan = plan.withinRange(low, hight);
==>
withinPlan = plan.withinRange(daysTempRange());
```

##### Replace Parameter with Methods（以函数取代参数）

对象调用某个函数，并将所得结果作为参数，传递给另一个函数。而接受该参数的函数本身也能够调用前一个函数。（让参数接受者去除该项参数，并直接调用前一个函数）

```java
int basePrice = _quantity * _itemPrice;
discountLevel = getDiscountLevel();
double finalPrice = discountedPrice(basePrice, discountLevel);
==>
int basePrice = _quantity * _itemPrice;
double finalPrice = discountedPrice(basePrice);
```

##### Introduce Parameter Object（引入参数对象）

某些参数总是很自然地同时出现。以一个对象取代这些参数。

```java
class Customer {
  amountInvoicedIn(start:Date, end: Date);
  amountReceived(start:Date, end: Date);
  amountOverdueln(start:Date, end: Date);
}
==>
class Customer {
  amountInvoicedIn(DateRange);
  amountReceived(DateRange);
  amountOverdueln(DateRange);
}
```

##### Remove Setting Method（移除设值函数）

类中的某个字段应该在对象创建时候被设值，然后就不再改变。（去掉该字段的所有设值函数）

```java
class Employee {
  Immutable;
  setImmutableValue()}
==>
class Employee {}
```

##### Hide Method（隐藏函数）

有一个函数，从来没有被其他任何类用到。（将这个函数修改为private）

##### Replace Constructor with Factory Method（以工厂函数取代构造函数）

你希望在创建对象时不仅仅是做简单的构建动作。（将构造函数替换为工厂函数。）

```java
Employee(int type) {
  _type = type
}
==>
static Employee create(int type) {
  return new Employee(type);
}
```

##### Encapsulate Downcast（封装向下转型）

某个函数返回的对象，需要由函数调用者执行向下转型(downcast)。（将向下转型动作移到函数中）

```java
Object lastReading() {
  return readings.lastElement();
}
==>
Reading lastReading() {
  return (Reading) readings.lastElement();
}
```

##### Replace Error Code with Exception（以异常取代错误码）

某个函数返回一个特定的代码，用以表示某种错误情况。（改用异常）

```java
int withdraw(int amount) {
  if (amount > _balance) {
    return -1;
  } else {
    _balance -= amount;
    return 0;
  }
}
==>
void withdraw(int amount) throws BalanceException {
  if (amount > _balance) {
    throw new BalanceException();
  }
  _balance -= amount;
}
```

##### Replace Exception with Test（以测试取代异常）

面对一个调用者可以预先检查的条件，你抛出了一个异常。(修改调用者，使它在调用函数之前先做检查)

```java
double getValueForPeriod(int periodNumber) {
  try {
    return _values[periodNumber];
  } catch(ArrayIndexOutOfBoundsException e) {
    return 0;
  }
}
==>
double getValueForPeriod(int periodNumber) {
  if (periodNumber >= _values.length) {
    return 0;
  }
  return _values[periodNumber];
}
```



### 处理概括关系

##### Pull Up Field（字段上移）

两个子类拥有相同的字段。（将该字段移至超类）

##### Pull Up Method（函数上移）

有些函数，在各个子类中产生完全相同的结果。（将该函数移至超类）

##### Pull Up Constructor Body（构造函数本体上移）

你在各个子类中拥有一些构造函数，它们的本体几乎完全一致。（在超类中新建一个构造函数，并在子类的构造函数中调用它）

```java
class Manager extends Employee {
  public Manager(String name,String id,int grade) {
    _name = name;
    _id = id;
    _grade = grade;
  }
}
==>
public Manager(String name, String id, int grade) {
  super(name, id);
  _grade = grade;
}
```

##### Push Down Method（函数下移）

超类中的某个函数只与部分（而非全部）子类有关。（将这个函数移到相关子类去）

##### Push Down Field（字段下移）

超类中的某个字段只被部分（而非全部）子类用到。（将这个字段移到需要它的那些子类去）

##### Extract Subclass（提炼子类）

类中的某些特性只被某些（而非全部）实例用到。（新建一个子类，将上面所说的那一部分特性移到子类中）

##### Extract SuperClass（提炼超类）

两个类有相似特性。（为这两个类建立一个超类，将相同特性移至超类）

##### Extract Interface（提炼接口）

若干客户使用类接口中的同一子集，或者两个类的接口有部分相同。（将相同的子集提炼到一个独立接口中）

##### Collapse Hierarchy（折叠继承体系）

超类和子类之间无太大区别。（将它们合为一体）

##### Form Template Method（塑造模板函数）

你有一些子类，其中相应的某些函数以相同顺序执行类似的操作，但各个操作的细节上有所不同。（将这些操作分别放进独立函数中，并保持它们都有相同的签名，于是原函数也就变得相同了。然后将原函数上移至超类）

##### Replace Inheritance with Delegation（以委托取代继承）

某个子类只使用超类接口中的一部分，或是根本不需要继承而来的数据。（在子类中新建一个字段用以保存超类；调整子类函数，令它改而委托超类；然后去掉两者之间的继承关系）

##### Replace Delegation with Inheritance（以继承取代委托）

你在两个类之间使用委托关系，并经常为整个接口编写许多极简单的委托函数。（让委托类继承受托类）



### 大型重构

##### Tease Apart Inheritance（梳理并分解继承体系）

某个继承体系同时承担两项责任。（建立两个继承体系，并通过委托关系让其中一个可以调用另一个）

##### Convert Procedural Design to Objects（将过程化设计转化为对象设计）

你手上有一些传统过程化风格的代码。（将数据记录变成对象，将大块的行为分成小块，并将行为移入相关对象之中。）

##### Separate Domain from Presentation（将领域和表述、显示分离）

某些GUI类之中包含了领域逻辑。（将领域逻辑分离出来，为它们建立独立的领域类）

```java
class OrderWindow {}
==>
class OrderWindow{}
class Order{}
```

##### Extract Hierarchy（提炼继承体系）

你有某个类做了太多工作，其中一部分工作是以大量条件表达式完成的。（建立继承体系，以一个子类表示一特殊情况）

