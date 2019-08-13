---
title: java-时间格式化
date: 2019-08-13 14:28:58
tags: [java,SimpleDateFormat]
---

#### SimpleDateFormat

```java
SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
```

<!--more-->

<br/>



##### java格式化字母表

| Symbol | Meaning              | Presentation  | Example               |
| :----: | :------------------- | :-----------: | :-------------------- |
|   G    | era designator       |     Text      | AD                    |
|   y    | year                 |    Number     | 2009                  |
|   M    | month in year        | Text & Number | July & 07             |
|   d    | day in month         |    Number     | 10                    |
|   h    | hour in am/pm (1-12) |    Number     | 12                    |
|   H    | hour in day (0-23)   |    Number     | 0                     |
|   m    | minute in hour       |    Number     | 30                    |
|   s    | second in minute     |    Number     | 55                    |
|   S    | millisecond          |    Number     | 978                   |
|   E    | day in week          |     Text      | Tuesday               |
|   D    | day in year          |    Number     | 189                   |
|   F    | day of week in month |    Number     | 2 (2nd Wed in July)   |
|   w    | week in year         |    Number     | 27                    |
|   W    | week in month        |    Number     | 2                     |
|   a    | am/pm marker         |     Text      | PM                    |
|   k    | hour in day (1-24)   |    Number     | 24                    |
|   K    | hour in am/pm (0-11) |    Number     | 0                     |
|   z    | time zone            |     Text      | Pacific Standard Time |
|   Z    | time zone            |     Text      | RFC 822               |
|   '    | escape for text      |   Delimiter   | (none)                |
|   '    | single quote         |    Literal    | '                     |