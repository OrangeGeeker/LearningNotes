# Calendar

Calendar.getInstance() 返回的是 Gregorian Calendar

```
部分字段
YEAR
MONTH
WEEK_OF_YEAR
DATE
DAY_OF_MONTH 和 DATE 值一样
DAY_OF_YEAR
DAY_OF_WEEK
DAY_OF_WEEK_IN_MONTH
WEEK_OF_MONTH
AM_PM
HOUR
HOUR_OF_DAY
MINUTE
SECOND
MILLISECOND
```

```java
Calendar instance = Calendar.getInstance();
System.out.println(instance.get(Calendar.YEAR)+"年");
System.out.println((instance.get(Calendar.MONTH)+1)+"月");
System.out.println(instance.get(Calendar.DATE)+"日");
System.out.println(instance.get(Calendar.HOUR_OF_DAY)+"时"); //24小时制

switch (instance.get(Calendar.AM_PM)) {
case Calendar.AM:
    System.out.println("上午");
    break;
case Calendar.PM:
    System.out.println("下午");
    break;
}

System.out.println(instance.get(Calendar.MINUTE)+"分");
System.out.println(instance.get(Calendar.SECOND)+"秒");
System.out.println(instance.get(Calendar.MILLISECOND)+"毫秒");

```

## ical4j

iCalendar component 包括 VEVENT VTODO VJOURNAL VFREEBUSY VTIMEZONE VALARM

核心对象第一行必须是“BEGIN:VCALENDAR”, 并且最后行必须是“END:VCALENDAR”。在这两行之间主要是由一系列日历属性和一个或者多个 iCalendar 组件组成。

```
BEGIN:VCALENDAR                   ------ 起始
VERSION:2.0                       ------ 版本
PRODID:iCal4j v1.0EN              ------ 创建该对象的标志符
BEGIN:VEVENT                      ------ 事件开始
DTSTART:19970714T170000Z          ------ 事件起始时间
DTEND:19970715T040000Z            ------ 事件结束时间
SUMMARY:Bastille Day Party        ------ 事件概要
END:VEVENT                        ------ 事件结束
END:VCALENDAR                     ------ 结束
```

### 读写示例

```java
public static void readAndWrite(String in, String out)
throws IOException, ParserException, ValidationException {
FileInputStream fin = new FileInputStream(in);
CalendarBuilder builder = new CalendarBuilder();
Calendar calendar = builder.build(fin);

...

FileOutputStream fout = new FileOutputStream(out);
CalendarOutputter outputter = new CalendarOutputter();
outputter.output(calendar, fout);
}
```

### 索引

```java
  // 创建索引列表
IndexedComponentList indexedEvents = new IndexedComponentList(
     myCalendar.getComponents(Component.VEVENT), Property.UID);

// 检查事件
for (Iterator i=inputCalendar.getComponents(Component.VEVENT).iterator(); i.hasNext();){
VEvent event = (VEvent) i.next();
Component existing = indexedEvents.getComponent(event.getUid().getValue());
if (existing == null) {
    myCalendar.getComponents().add(event);
}
else if (!event.equals(existing)) {
    // 删除已经存在的事件并添加修改后的事件
    myCalendar.getComponents().remove(existing);
    myCalendar.getComponents().add(event);
}
}
```

### 循环事件

iCalendar 规范支持循环事件，即事件不止一次发生，通常使用一系列的日期（RDATE）或者一个循环规则（RRULE）。

循环日期 RDate

```java
public static void addRDate(String subject, String out)
throws IOException, ParserException, ValidationException,
URISyntaxException, ParseException {
Calendar calendar = new Calendar();
calendar.getProperties().add(new ProdId("-//Ben Fortuna//iCal4j 1.0//EN"));
calendar.getProperties().add(Version.VERSION_2_0);
calendar.getProperties().add(CalScale.GREGORIAN);

PeriodList periodList = new PeriodList();
ParameterList paraList = new ParameterList();
DateFormat format = new SimpleDateFormat("MM/dd/yyyy hh:mm");
DateTime startDate1 = new DateTime(format.parse(("11/09/2009 08:00")));
DateTime startDate2 = new DateTime(format.parse(("11/10/2009 09:00")));
DateTime endDate1 = new DateTime(format.parse(("11/09/2009 09:00")));
DateTime endDate2 = new DateTime(format.parse(("11/10/2009 11:00")));
periodList.add(new Period(startDate1, endDate1));
periodList.add(new Period(startDate2, endDate2));

VEvent event = new VEvent(startDate1, endDate1, subject);
event.getProperties().add(new Uid(new UidGenerator("iCal4j").
generateUid().getValue()));
paraList.add(ParameterFactoryImpl.getInstance().createParameter(
Value.PERIOD.getName(), Value.PERIOD.getValue()));
RDate rdate = new RDate(paraList,periodList);
event.getProperties().add(rdate);
calendar.getComponents().add(event);

// 验证
calendar.validate();

FileOutputStream fout = new FileOutputStream(out);
CalendarOutputter outputter = new CalendarOutputter();
outputter.output(calendar, fout);
}
```

循环事件 RRule

```java
public static void addRRule(String subject, String out)
    throws IOException, ParserException, ValidationException,
URISyntaxException, ParseException {
Calendar calendar = new Calendar();
DateFormat format = new SimpleDateFormat("MM/dd/yyyy HH:mm");
DateTime start = new DateTime(format.parse("11/09/2009 08:00").getTime());
DateTime end = new DateTime(format.parse("11/09/2009 09:00").getTime());
calendar.getProperties().add(new ProdId("-//Ben Fortuna//iCal4j 1.0//EN"));
calendar.getProperties().add(Version.VERSION_2_0);
calendar.getProperties().add(CalScale.GREGORIAN);

VEvent event = new VEvent(start, end, subject);
event.getProperties().add(new Uid(new UidGenerator("iCal4j").generateUid()
.getValue()));

Recur recur = new Recur(Recur.WEEKLY, 4);
recur.setInterval(2);
RRule rule = new RRule(recur);
event.getProperties().add(rule);
calendar.getComponents().add(event);

// 验证
calendar.validate();

FileOutputStream fout = new FileOutputStream(out);
CalendarOutputter outputter = new CalendarOutputter();
outputter.output(calendar, fout);
}
```

### 扩展

通常情况下我们把没有在 RFC2445 中没有定义的组件（components）/属性（properties）/参数（parameters）  都称为非标准的或者扩展的对象。

iCalendar 允许用户扩展组件/属性/参数，但是名字必须以 X- 开头以兼容 iCalendar 标准  （除非使能 ical4j.parsing.relaxed），这些对象分别用 XComponent, XProperty and XParameter 来表示。

```java
public static void extension(String subject, String out)    throws IOException, ParserException, ValidationException, URISyntaxException, ParseException {
Calendar calendar = new Calendar();
DateFormat format = new SimpleDateFormat("MM/dd/yyyy HH:mm");
DateTime start = new DateTime(format.parse("11/09/2009 08:00").getTime());
DateTime end = new DateTime(format.parse("11/09/2009 09:00").getTime());
calendar.getProperties().add(new ProdId("-//Ben Fortuna//iCal4j 1.0//EN"));
calendar.getProperties().add(Version.VERSION_2_0);
calendar.getProperties().add(CalScale.GREGORIAN);

VEvent event = new VEvent(start, end, subject);
event.getProperties().add(new Uid(new UidGenerator("iCal4j")
.generateUid().getValue()));
// 注意：扩展的属性必须以 X- 开头
Property xProperty = PropertyFactoryImpl.getInstance()
.createProperty("X-iCal4j-extension");
xProperty.setValue("ical4j_extension_sample");
event.getProperties().add(xProperty);
calendar.getComponents().add(event);
// 验正
calendar.validate();

FileOutputStream fout = new FileOutputStream(out);
CalendarOutputter outputter = new CalendarOutputter();
outputter.output(calendar, fout);
}
```

### 添加二进制

iCal4j 代码库允许用户添加二进制数据，类 Attach 可以被用来实现二进制数据的添加

```java
public static void attachBinaryAttachment(String subject, String out, String binary)
    throws IOException, ParserException, ValidationException, ParseException {
FileInputStream bin = new FileInputStream(binary);
ByteArrayOutputStream bout = new ByteArrayOutputStream();
Calendar calendar = new Calendar();
DateFormat format = new SimpleDateFormat("MM/dd/yyyy HH:mm");
DateTime start = new DateTime(format.parse("11/09/2009 08:00").getTime());
DateTime end = new DateTime(format.parse("11/09/2009 09:00").getTime());
calendar.getProperties().add(new ProdId("-//Ben Fortuna//iCal4j 1.0//EN"));
calendar.getProperties().add(Version.VERSION_2_0);
calendar.getProperties().add(CalScale.GREGORIAN);

VEvent event = new VEvent(start, end, subject);
event.getProperties().add(new Uid(new UidGenerator("iCal4j")
.generateUid().getValue()));
for (int i = bin.read(); i >= 0;) {
    bout.write(i);
  i = bin.read();
}
ParameterList params = new ParameterList();
params.add(Encoding.BASE64);
params.add(Value.BINARY);
Attach attach = new Attach(params, bout.toByteArray());
event.getProperties().add(attach);
calendar.getComponents().add(event);

// 验证
calendar.validate();

FileOutputStream fout = new FileOutputStream(out);
CalendarOutputter outputter = new CalendarOutputter();
outputter.output(calendar, fout);
}
```

### 时间时区

iCal4j 提供了自己的 Date/Time 和 TimeZone 实现，与之相关的类主要有 Date、DateTime、TimeZone、 TimeZoneRegistry、ZoneInfo 和 TzUrl 等。

Date 对象表示一个日期（不包括时间），而 DateTime 对象则表示日期（包含时间）。如果表示一个 UTC 时间，则只需要在时间之后加上一个“Z ”，如 DTSTART:19980119T070000Z。另外，也可以对一个时间指定一个时区，例如 DTSTART;TZID=US-Eastern:19980119T020000。

TimeZoneRegistry 则表示 net.fortuna.ical4j.model.TimeZone 句柄的一个仓库（Repository）。清单 4 演示了如何获取一个 TimeZoneRegistry，并通过 TimeZoneRegistry 来获取一个时区（TimeZone）。

```java
CalendarBuilder builder = new CalendarBuilder();
Calendar calendar = builder.build(new FileInputStream("mycalendar.ics"));

TimeZoneRegistry registry = builder.getRegistry();
TimeZone tz = registry.getTimeZone("Australia/Melbourne");
```

## DEMO

### 解析文件

```java
FileInputStream fin = new FileInputStream("mycalendar.ics");

CalendarBuilder builder = new CalendarBuilder();

Calendar calendar = builder.build(fin);
```

### 解析字符串

```java
String myCalendarString = ...

StringReader sin = new StringReader(myCalendarString);

CalendarBuilder builder = new CalendarBuilder();

Calendar calendar = builder.build(sin);
```

### 创建 calendar

```java
Calendar calendar = new Calendar();
calendar.getProperties().add(new ProdId("-//Ben Fortuna//iCal4j 1.0//EN"));
calendar.getProperties().add(Version.VERSION_2_0);
calendar.getProperties().add(CalScale.GREGORIAN);

// Add events, etc..

Output:

BEGIN:VCALENDAR
PRODID:-//Ben Fortuna//iCal4j 1.0//EN
VERSION:2.0
CALSCALE:GREGORIAN
END:VCALENDAR
```

### 创建全天事件

```java
java.util.Calendar calendar = java.util.Calendar.getInstance();
calendar.set(java.util.Calendar.MONTH, java.util.Calendar.DECEMBER);
calendar.set(java.util.Calendar.DAY_OF_MONTH, 25);

// initialise as an all-day event..
VEvent christmas = new VEvent(new Date(calendar.getTime()), "Christmas Day");

// Generate a UID for the event..
UidGenerator ug = new UidGenerator("1");
christmas.getProperties().add(ug.generateUid());

net.fortuna.ical4j.model.Calendar cal = new net.fortuna.ical4j.model.Calendar();
cal.getComponents().add(christmas);

Output:

BEGIN:VEVENT
DTSTAMP:20050222T044240Z
DTSTART;VALUE=DATE:20051225
SUMMARY:Christmas Day
UID:20050222T044240Z-1@example.com
END:VEVENT
```

### 创建时间段

```java
// 创建一个时区（TimeZone）
TimeZoneRegistry registry = TimeZoneRegistryFactory.getInstance().createRegistry();
TimeZone timezone = registry.getTimeZone("America/Mexico_City");
VTimeZone tz = timezone.getVTimeZone();

// 起始时间是：2008 年 4 月 1 日 上午 9 点
java.util.Calendar startDate = new GregorianCalendar();
startDate.setTimeZone(timezone);
startDate.set(java.util.Calendar.MONTH, java.util.Calendar.APRIL);
startDate.set(java.util.Calendar.DAY_OF_MONTH, 1);
startDate.set(java.util.Calendar.YEAR, 2008);
startDate.set(java.util.Calendar.HOUR_OF_DAY, 9);
startDate.set(java.util.Calendar.MINUTE, 0);
startDate.set(java.util.Calendar.SECOND, 0);

// 结束时间是：2008 年 4 月 1 日 下午 1 点
java.util.Calendar endDate = new GregorianCalendar();
endDate.setTimeZone(timezone);
endDate.set(java.util.Calendar.MONTH, java.util.Calendar.APRIL);
endDate.set(java.util.Calendar.DAY_OF_MONTH, 1);
endDate.set(java.util.Calendar.YEAR, 2008);
endDate.set(java.util.Calendar.HOUR_OF_DAY, 13);
endDate.set(java.util.Calendar.MINUTE, 0);
endDate.set(java.util.Calendar.SECOND, 0);

// 创建事件
String eventName = "Progress Meeting";
DateTime start = new DateTime(startDate.getTime());
DateTime end = new DateTime(endDate.getTime());
VEvent meeting = new VEvent(start, end, eventName);

// 添加时区信息
meeting.getProperties().add(tz.getTimeZoneId());

// 生成唯一标志符
UidGenerator ug = new UidGenerator("uidGen");
Uid uid = ug.generateUid();
meeting.getProperties().add(uid);

// 添加参加者 .
Attendee dev1 = new Attendee(URI.create("mailto:dev1@mycompany.com"));
dev1.getParameters().add(Role.REQ_PARTICIPANT);
dev1.getParameters().add(new Cn("Developer 1"));
meeting.getProperties().add(dev1);

Attendee dev2 = new Attendee(URI.create("mailto:dev2@mycompany.com"));
dev2.getParameters().add(Role.OPT_PARTICIPANT);
dev2.getParameters().add(new Cn("Developer 2"));
meeting.getProperties().add(dev2);

// 创建日历
net.fortuna.ical4j.model.Calendar icsCalendar = new net.fortuna.ical4j.model.Calendar();
icsCalendar.getProperties().add(new ProdId("-//Events Calendar//iCal4j 1.0//EN"));
icsCalendar.getProperties().add(CalScale.GREGORIAN);

// 添加事件
icsCalendar.getComponents().add(meeting);
```

### 生成文件

```java
ileOutputStream fout = new FileOutputStream("mycalendar.ics");

CalendarOutputter outputter = new CalendarOutputter();
outputter.output(calendar, fout);
```

### 添加二进制数据

```java
FileInputStream fin = new FileInputStream("etc/artwork/logo.png");
ByteArrayOutputStream bout = new ByteArrayOutputStream();
for (int i = fin.read(); i >= 0;) {
    bout.write(i);
    i = fin.read();
}

ParameterList params = new ParameterList();
params.add(Value.BINARY);
params.add(Encoding.BASE64);

Attach attach = new Attach(params, bout.toByteArray());

Output:

ATTACH;ENCODING=BASE64;VALUE=BINARY:iVBORw0KGgoAAAANSUhEUgAAAUYAAACJCAYAA
 ABQHpElAAAABHNCSVQICAgIfAhkiAAAABl0RVh0U29mdHdhcmUAd3d3Lmlua3NjYXBlLm9yZ
 5vuPBoAABPKSURBVHic7d15tFX1dcDx72aQyQkMqAiIojjFASc0GmOlRqKmcR6XpkaJYtrU1
 LYSp0ZZqxo1SZ2CUZPGqlXRGEdAqzKoqGjqCKgoVFF8DkVEBJRh9499HiGXB /ec3/nnN89d
 3/WOmvx9J599r3vvf3O8Pvtn6gqzrkaiGwPzCw6jTZsjmrLqq9ERgGXFZdOmyajeuCqr0QOB
 ...
```

### 过滤事件

```java
java.util.Calendar today = java.util.Calendar.getInstance();
today.set(java.util.Calendar.HOUR_OF_DAY, 0);
today.clear(java.util.Calendar.MINUTE);
today.clear(java.util.Calendar.SECOND);

// create a period starting now with a duration of one (1) day..
Period period = new Period(new DateTime(today.getTime()), new Dur(1, 0, 0, 0));
Filter filter = new Filter(new PeriodRule(period));

List eventsToday = filter.filter(calendar.getComponents(Component.VEVENT));
```

### 生成事件时间

```java
// Reading the file and creating the calendar
CalendarBuilder builder = new CalendarBuilder();
Calendar cal = null;
try {
    cal = builder.build(new FileInputStream("my.ics"));
} catch (IOException e) {
    e.printStackTrace();
} catch (ParserException e) {
    e.printStackTrace();
}


// Create the date range which is desired.
DateTime from = new DateTime("20100101T070000Z");
DateTime to = new DateTime("20100201T070000Z");;
Period period = new Period(from, to);


// For each VEVENT in the ICS
for (Object o : cal.getComponents("VEVENT")) {
    Component c = (Component)o;
    PeriodList list = c.calculateRecurrenceSet(period);

    for (Object po : list) {
        System.out.println((Period)po);
    }
}
```

### 创建 vCard

```java
List<Property> props = new ArrayList<Property>();
props.add(new Source(URI.create("ldap://ldap.example.com/cn=Babs%20Jensen,%20o=Babsco,%20c=US")));
props.add(new Name("Babs Jensen's Contact Information"));
props.add(Kind.INDIVIDUAL);
// add a custom property..
props.add(new Property("test") {
    @Override
    public String getValue() {
        return null;
    }

    @Override
    public void validate() throws ValidationException {
    }
});

VCard vcard = new VCard(props);
```