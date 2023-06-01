# 死磕 Java 日期处理细节

[toc]

## 简介

伴随着`lambda表达式`、`streams`以及时间微小优化，Java 8 推出了全新的日期 API。

Java 日期和时间的安全不足：java.util.Date 设置为增加新的特性日期，以及简单的应用日期和非应用日历格式。

全新API的时间概念之一就是，明显的日期，例如：`瞬时（instant）`、`长短（duration）`、`日期`、和。`时间``时区``周期`

继承库按人类语言和计算机类解析的时间处理方式。Joda不同于旧标准版本，新API基于日历系统，java.time包下的所有类型都没有任何线程安全性。

## 关键类

- Instant：瞬间实例。
- LocalDate：本地日期，不包含具体时间 例如：2014-01-14 可以使用记录生日、纪念日、加盟日等。
- LocalTime：本地时间，不包含日期。
- LocalDateTime：组合了日期和时间，但不包含时差和时区信息。
- ZonedDateTime：最完整的日期时间，包含时区和相对UTC或格林威治的时差。

新API改了 ZoneOff 和 ZoneId 解决时区问题，更多的日期和时间类型。TimeFormatter 类也解析、重新设计。

## 实战

在教程中将通过一些实例来学习如何使用新的，只有在简单的实际项目中使用API，我们才能学习新知识以及新知识以及最新的教程。

### 1. 获取当前的日期

Java 8 中的`LocalDate`用于当天日期。和 java.util.Date 不同，它有日期，不包含时间。当你只需要表示日期时就用这个类。

```java
//获取今天的日期
public void getCurrentDate(){
    LocalDate today = LocalDate.now();
    System.out.println("Today's Local date : " + today);

    //这个是作为对比
    Date date = new Date();
    System.out.println(date);
}
```

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)图片

上面的日期显示日期，打印日期的日期信息。打印出日期的日期，查看日期非常没有显示类型的信息。

### 2.获取年、月、日信息

`LocalDate`提供了获取年、月、日的快捷方法，其实例还包含许多其他的日期属性。通过调用这些类方法就可以很方便地得到需要的日期信息，不用以前需要依赖 java.util.Calendar 了。

```Java
//获取年、月、日信息
public void getDetailDate(){
    LocalDate today = LocalDate.now();
    int year = today.getYear();
    int month = today.getMonthValue();
    int day = today.getDayOfMonth();

    System.out.printf("Year : %d  Month : %d  day : %d t %n", year, month, day);
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tZ4IBiaCXw5nDON6U1GukMF6OzJzFTf3WX5tTqXBjqPuB4oKZkHa7ibeQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 3. 处理特定日期

在第一个实例中，我们通过参数真实地过去了当天的`LocalDate.of()`日期。返回的日期实例。这个好处是没有再犯设计错误，比如当年的 API 方法是在 1900 年以前看到的，从当年`0`开始等等。 ，直接明了。

```
//处理特定日期
public void handleSpecilDate(){
    LocalDate dateOfBirth = LocalDate.of(2018, 01, 21);
    System.out.println("The specil date is : " + dateOfBirth);
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tWrfN1LN97CVv97Ms97EkdeMwXAiacNZ8JTgKEYaA33IKa3WWA3qz5vw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 4. 确定两个日期

实践项目帮助解决这个问题的例子就是确定两个日期是否处理的例子。在开发过程中遇到这样子的问题`LocalDate`。 ，如果比较的日期是日期类型的，需要先解析成对象再作判断。

请看下面的例子：

```
//判断两个日期是否相等
public void compareDate(){
    LocalDate today = LocalDate.now();
    LocalDate date1 = LocalDate.of(2018, 01, 21);

    if(date1.equals(today)){
           System.out.printf("TODAY %s and DATE1 %s are same date %n", today, date1);
    }
}
```


![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)图片

### 5.检查像生日过生日事件

Java中另一天时间的处理类似、生日纪念日、节日假期（或每个月固定时间的客户就是经常检查）。呢？这些答案就是`MonthDay`类。这个类组合了它和日，都去掉了，这意味着你可以每年判断也发生类似的事件`YearMonth`。安全的类型下面我们通过`MonthDay`来检查事件。

```
//处理周期性的日期
public void cycleDate(){
    LocalDate today = LocalDate.now();
    LocalDate dateOfBirth = LocalDate.of(2018, 01, 21);

    MonthDay birthday = MonthDay.of(dateOfBirth.getMonth(), dateOfBirth.getDayOfMonth());
    MonthDay currentMonthDay = MonthDay.from(today);

    if(currentMonthDay.equals(birthday)){
       System.out.println("Many Many happy returns of the day !!");
    }else{
       System.out.println("Sorry, today is not your birthday");
    }
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tDPiaiae2KaRdeOptXKhaDpSzDVQv0uXYVMsguXYRSCFdd4aOV5dKgJmQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 6.获取当前时间

与日期的例子很像，获取时间使用的是`LocalTime`类，只有一个时间没有本地日期的近亲。可以调用获取静态工厂方法now()来获取当前时间。默认的格式是`hh:mm:ss:nnn`。

```
//获取当前时间
public void getCurrentTime(){
    LocalTime time = LocalTime.now();
    System.out.println("local time now : " + time);
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tMRq0QB8YdamzOywxgc4OWNLphktg2vTC02lT53Wd9QHyDpDOVF4LOA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 7.在现有的时间上增加小时数

Java 8 这些提供了更好的替代方法 add()，并且实例是全新的方法。

```
//增加小时
public void plusHours(){
    LocalTime time = LocalTime.now();
    LocalTime newTime = time.plusHours(2); // 增加两小时
    System.out.println("Time after 2 hours : " +  newTime);
}
```

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)图片

### 8.如何计算一周之后的日期

和上个例子计算两小时以后的时间类似，这个例子计算一周后的日期。LocalDate Date不包含时间信息，它的plus()方法使用增加天、周、月，ChronoUnit类声明了时间单位由于D不变，返回后一定要用变化类型。

可以使用同样的方法增加 1 个月、1 年、1 小时、1 分钟甚至一个世纪，更多选项可以查看 Java 8 API 中的 ChronoUnit 类。

```
//如何计算一周后的日期
public void nextWeek(){
    LocalDate today = LocalDate.now();
    LocalDate nextWeek = today.plus(1, ChronoUnit.WEEKS);    //使用变量赋值
    System.out.println("Today is : " + today);
    System.out.println("Date after 1 week : " + nextWeek);
}
```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/knmrNHnmCLETkShoD4ZD0kJlX9yo279bhH6JtQcDibBjgCEpyqEicoFLOecjd0WmzZr4F8bKTj2yIbna4TICtBibQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 9.计算这一年之前或之后的日期

这个例子中我们通过增加天数、周数或月数，这个例子是我们利用我们`LocalDate`的方法计算一年前的日期。`plus()``minus()`

```
//计算一年前或一年后的日期
public void minusDate(){
    LocalDate today = LocalDate.now();
    LocalDate previousYear = today.minus(1, ChronoUnit.YEARS);
    System.out.println("Date before 1 year : " + previousYear);

    LocalDate nextYear = today.plus(1, ChronoUnit.YEARS);
    System.out.println("Date after 1 year : " + nextYear);
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tanQSzYd5vO2cDno7RPkibGC2HUC8ZoOiang7bibfrc2h2pQiaV2TDbtVVg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 10.使用Java 8的Clock时钟类

Java 的时钟类获取当时的日期，用于当前时间的时钟信息。以前的一个时钟区或下一个时钟。

```
public void clock(){
    // 根据系统时间返回当前时间并设置为UTC。
    Clock clock = Clock.systemUTC();
    System.out.println("Clock : " + clock);

    // 根据系统时钟区域返回时间
    Clock defaultClock = Clock.systemDefaultZone();
    System.out.println("Clock : " + clock);
}
```

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)图片

### 11. 鉴定日期是早于还是晚于另一个日期

LocalDate 则有类`isBefore()`使用`isAfter()`日期`isBefore()`。

```
//如何用Java判断日期是早于还是晚于另一个日期
public void isBeforeOrIsAfter(){
    LocalDate today = LocalDate.now();

    LocalDate tomorrow = LocalDate.of(2018, 1, 29);
    if(tomorrow.isAfter(today)){
        System.out.println("Tomorrow comes after today");
    }

    //减去一天
    LocalDate yesterday = today.minus(1, ChronoUnit.DAYS);

    if(yesterday.isBefore(today)){
        System.out.println("Yesterday is day before today");
    }
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tPDgYhZGcbqgJlQK0VKen004SNVmue1ltE9TbeX8NIdURk8w1o5lM7Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 12.处理时区

Java 8 时间，也表示分开了日期和把时间分开出来了。现在有个人的区域类，例如 ZoneId 来处理特定时间区域，ZoneDateTime 类来某个时间区域下的时间。

```
//获取特定时区下面的时间
public void getZoneTime(){
    //设置时区
    ZoneId america = ZoneId.of("America/New_York");

    LocalDateTime localtDateAndTime = LocalDateTime.now();

    ZonedDateTime dateAndTimeInNewYork  = ZonedDateTime.of(localtDateAndTime, america );
    System.out.println("现在的日期和时间在特定的时区 : " + dateAndTimeInNewYork);
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99twxBKo61Qu1qLicLVlvBkMMjaFaLzy68yzH0vrMZag85ysicB3mTCqWkg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 13. 如何选择出日期固定

例如：表示信用卡可以用来确定日期的类似，用于表示重复事件`YearMonth`、FD等日期与日期的相似性。类得到方法当月共有多少天，年月实例的可以当月`lengthOfMonth()`的天数，判断2月有28天还是29天时返回非常有用。

```
//使用 YearMonth类处理特定的日期
public void checkCardExpiry(){
    YearMonth currentYearMonth = YearMonth.now();
    System.out.printf("Days in month year %s: %d%n", currentYearMonth, currentYearMonth.lengthOfMonth());

    YearMonth creditCardExpiry = YearMonth.of(2028, Month.FEBRUARY);
    System.out.printf("Your credit card expires on %s %n", creditCardExpiry);
}
```

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)图片

### 14.检查闰年

LocalDate类有一个很实用的方法`isLeapYear()`判断该实例是否是一个闰年，如果你想重新发明一个程序，这有一个代码示例，纯Java写的判断闰年的还是。

```
//检查闰年
public void isLeapYear(){
    LocalDate today = LocalDate.now();
    if(today.isLeapYear()){
        System.out.println("This year is Leap year");
    }else {
        System.out.println("2018 is not a Leap year");
    }
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tic1jVhibBtRK9pIWGe6Et4pNl3dSVzNibwZicvWSybD380y3gGic40s9BWQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 15.计算两个日期之间的天数和月数

在 Java 8 天中可以用 java.time.time.time.Period 类来计算两个日期之间的经常数字。下面的例子：现在是一月份，距离到五月份，中间相隔3月

```
//计算两个日期之间的天数和月数
public void calcDateDays(){
    LocalDate today = LocalDate.now();

    LocalDate java8Release = LocalDate.of(2018, Month.MAY, 14);

    Period periodToNextJavaRelease = Period.between(today, java8Release);

    System.out.println("Months left between today and Java 8 release : "
                                           + periodToNextJavaRelease.getMonths() );
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tFmbbCf0yB9fficHPhYQ1UvoBrjA8NyUfCxxsNAtXDfH1F8rBIsSQ5ag/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

### 16.包含时差信息的日期和时间

ZoneOffset 使用时区时差和 GMT+05:30 的区域，通过()方法来获取时差。来创建一个OffSetDateTime对象。

```
public void ZoneOffset(){
    LocalDateTime datetime = LocalDateTime.of(2018, Month.FEBRUARY, 14, 19, 30);
    ZoneOffset offset = ZoneOffset.of("+05:30");
    OffsetDateTime date = OffsetDateTime.of(datetime, offset);
    System.out.println("Date and Time with timezone offset in Java : " + date);
}
```

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)图片

### 17. 获取本地的葡萄

即时类有一个对象类型：now()会返回当前的方法，如下所示

```
public void getTimestamp(){
    Instant timestamp = Instant.now();
    System.out.println("What is value of this instant " + timestamp);
}
```

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)图片

### 18. 修改或修改日期定义的工具去解析或修改日期

Java 8更新了全新的日期时间格式工具，线程安全。它带有一些常用的格式。下面的BASIC_ISO_DATE工具可以使用这个工具将2018年210日格式化成20180210。

```
// 使用预定义的格式化工具去解析或格式化日期
public void formateDate(){
    String dayAfterTommorrow = "20180210";
    LocalDate formatted = LocalDate.parse(dayAfterTommorrow, DateTimeFormatter.BASIC_ISO_DATE);
    System.out.printf("Date generated from String %s is %s %n", dayAfterTommorrow, formatted);
}
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/6mychickmupWdx1iaqrRl512WdRqG9c99tzNV2jic9icQJWpkTXiauV39tExiaZUbqFiaWvhQlFud46mQib2mA8rIhN3rQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)图片

## 最后

最后附上全部代码

```Java
package com.wq.study.java8.date;

import java.time.Clock;
import java.time.Instant;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.Month;
import java.time.MonthDay;
import java.time.OffsetDateTime;
import java.time.ZoneOffset;
import java.time.Period;
import java.time.YearMonth;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;
import java.time.temporal.ChronoUnit;
import java.util.Date;

public class DateTest {

    //获取今天的日期
    public void getCurrentDate(){
        LocalDate today = LocalDate.now();
        System.out.println("Today's Local date : " + today);

        //这个是作为对比
        Date date = new Date();
        System.out.println(date);
    }

    //获取年、月、日信息
    public void getDetailDate(){
        LocalDate today = LocalDate.now();
        int year = today.getYear();
        int month = today.getMonthValue();
        int day = today.getDayOfMonth();

        System.out.printf("Year : %d  Month : %d  day : %d t %n", year, month, day);
    }

    //处理特定日期
    public void handleSpecilDate(){
        LocalDate dateOfBirth = LocalDate.of(2018, 01, 21);
        System.out.println("The specil date is : " + dateOfBirth);
    }

    //判断两个日期是否相等
    public void compareDate(){
        LocalDate today = LocalDate.now();
        LocalDate date1 = LocalDate.of(2018, 01, 21);

        if(date1.equals(today)){
            System.out.printf("TODAY %s and DATE1 %s are same date %n", today, date1);
        }
    }

    //处理周期性的日期
    public void cycleDate(){
        LocalDate today = LocalDate.now();
        LocalDate dateOfBirth = LocalDate.of(2018, 01, 21);

        MonthDay birthday = MonthDay.of(dateOfBirth.getMonth(), dateOfBirth.getDayOfMonth());
        MonthDay currentMonthDay = MonthDay.from(today);

        if(currentMonthDay.equals(birthday)){
           System.out.println("Many Many happy returns of the day !!");
        }else{
           System.out.println("Sorry, today is not your birthday");
        }
    }

    //获取当前时间
    public void getCurrentTime(){
        LocalTime time = LocalTime.now();
        System.out.println("local time now : " + time);
    }

    //增加小时
    public void plusHours(){
        LocalTime time = LocalTime.now();
        LocalTime newTime = time.plusHours(2); // 增加两小时
        System.out.println("Time after 2 hours : " +  newTime);
    }

    //如何计算一周后的日期
    public void nextWeek(){
        LocalDate today = LocalDate.now();
        LocalDate nextWeek = today.plus(1, ChronoUnit.WEEKS);
        System.out.println("Today is : " + today);
        System.out.println("Date after 1 week : " + nextWeek);
    }

    //计算一年前或一年后的日期
    public void minusDate(){
        LocalDate today = LocalDate.now();
        LocalDate previousYear = today.minus(1, ChronoUnit.YEARS);
        System.out.println("Date before 1 year : " + previousYear);

        LocalDate nextYear = today.plus(1, ChronoUnit.YEARS);
        System.out.println("Date after 1 year : " + nextYear);
    }

    public void clock(){
        // 根据系统时间返回当前时间并设置为UTC。
        Clock clock = Clock.systemUTC();
        System.out.println("Clock : " + clock);

        // 根据系统时钟区域返回时间
        Clock defaultClock = Clock.systemDefaultZone();
        System.out.println("Clock : " + clock);
    }

    //如何用Java判断日期是早于还是晚于另一个日期
    public void isBeforeOrIsAfter(){
        LocalDate today = LocalDate.now();

        LocalDate tomorrow = LocalDate.of(2018, 1, 29);
        if(tomorrow.isAfter(today)){
            System.out.println("Tomorrow comes after today");
        }

        LocalDate yesterday = today.minus(1, ChronoUnit.DAYS);

        if(yesterday.isBefore(today)){
            System.out.println("Yesterday is day before today");
        }
    }

    //时区处理
    public void getZoneTime(){
        //设置时区
        ZoneId america = ZoneId.of("America/New_York");

        LocalDateTime localtDateAndTime = LocalDateTime.now();

        ZonedDateTime dateAndTimeInNewYork  = ZonedDateTime.of(localtDateAndTime, america );
        System.out.println("现在的日期和时间在特定的时区 : " + dateAndTimeInNewYork);
    }

    //使用 YearMonth类处理特定的日期
    public void checkCardExpiry(){
        YearMonth currentYearMonth = YearMonth.now();
        System.out.printf("Days in month year %s: %d%n", currentYearMonth, currentYearMonth.lengthOfMonth());

        YearMonth creditCardExpiry = YearMonth.of(2028, Month.FEBRUARY);
        System.out.printf("Your credit card expires on %s %n", creditCardExpiry);
    }

    //检查闰年
    public void isLeapYear(){
        LocalDate today = LocalDate.now();
        if(today.isLeapYear()){
           System.out.println("This year is Leap year");
        }else {
            System.out.println("2018 is not a Leap year");
        }
    }

    //计算两个日期之间的天数和月数
    public void calcDateDays(){
        LocalDate today = LocalDate.now();

        LocalDate java8Release = LocalDate.of(2018, Month.MAY, 14);

        Period periodToNextJavaRelease = Period.between(today, java8Release);

        System.out.println("Months left between today and Java 8 release : "
                                           + periodToNextJavaRelease.getMonths() );
    }

    // 包含时差信息的日期和时间
    public void ZoneOffset(){
        LocalDateTime datetime = LocalDateTime.of(2018, Month.FEBRUARY, 14, 19, 30);
        ZoneOffset offset = ZoneOffset.of("+05:30");
        OffsetDateTime date = OffsetDateTime.of(datetime, offset);
        System.out.println("Date and Time with timezone offset in Java : " + date);
    }

    // 获取时间戳
    public void getTimestamp(){
        Instant timestamp = Instant.now();
        System.out.println("What is value of this instant " + timestamp);
    }

    // 使用预定义的格式化工具去解析或格式化日期
    public void formateDate(){
        String dayAfterTommorrow = "20180210";
        LocalDate formatted = LocalDate.parse(dayAfterTommorrow, DateTimeFormatter.BASIC_ISO_DATE);
        System.out.printf("Date generated from String %s is %s %n", dayAfterTommorrow, formatted);
    }

    public static void main(String[] args) {
        DateTest dt = new DateTest();

        dt.formateDate();
    }

}
```

## 总结

```
Java 8日期时间API的重点
1）提供了javax.time.ZoneId 获取时区。
2）提供了LocalDate和LocalTime类。
3）Java 8 的所有日期和时间API都是不可变类并且线程安全，而现有的Date和Calendar API中的java.util.Date和SimpleDateFormat是非线程安全的。
4）主包是 java.time,包含了表示日期、时间、时间间隔的一些类。里面有两个子包java.time.format用于格式化， java.time.temporal用于更底层的操作。
5）时区代表了地球上某个区域内普遍使用的标准时间。每个时区都有一个代号，格式通常由区域/城市构成（Asia/Tokyo），在加上与格林威治或 UTC的时差。例如：东京的时差是+09:00。
  
```