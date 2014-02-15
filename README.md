Time4J
======

Advanced date and time library for Java

Motivation:
-----------

Time4J is thought as a complete and high-end replacement for old java classes around java.util.Date, java.util.Calendar and java.text.SimpleDateFormat. This project is also intended as first-class alternative to the popular libraries JodaTime and its successor JSR-310 (Threeten) since the target audience of Time4J will not only be business Java developers, but also developers with a more scientific background (for example extended time scale support including leap seconds or historically accurate dates).

Although the new JSR-310 (built in Java 8) is certainly a very useful library for many business developers it also has some severe and intentional limitations. Time4J intends to fill all gaps in the future.

Current state and introduction:
-------------------------------

On 2014-02-15 I have published the first release of Time4J as initial version v0.1-alpha. A development process of more than two years has preceded this big milestone. Although this is just the begin of release cycles, Time4J is now really useable for many applications. Standard use cases will be covered by the main package "net.time4j". It offers four basic temporal types.

- PlainDate = calendar date strictly following ISO-8601

- PlainTime = wall time (on an analogous clock) including 24:00-support

- PlainTimestamp = local timestamp as composition of calendar date and wall time

- Moment = global timestamp which refers to true UTC standard including leapsecond-support

Here some examples as a flavour of how Time4J-code looks like:

```java
import net.time4j.*;
import net.time4j.tz.TransitionStrategy;
import static net.time4j.PlainTime.MINUTE_OF_HOUR;


  PlainTimestamp currentLocalTimestamp =
    SystemClock.inTimezone(TZID.EUROPE.BERLIN).now();
  PlainTime roundedTime =
    currentLocalTimestamp.getWallTime() // T22:06:52,688
                         .with(MINUTE_OF_HOUR.atFloor()) // T22:06
                         .with(MINUTE_OF_HOUR.roundedDown(5)); // T22:05

  Moment leapsecondUTC =
    PlainDate.of(2012, Month.JUNE, 30)
    .atTime(PlainTime.atEndOfDay()) // 2012-06-30T24 => 2012-07-01T00
    .inTimezone(ZonalOffset.UTC, TransitionStrategy.PUSH_FORWARD)
    .minus(1, SI.SECONDS);
  System.out.println(leapsecondUTC); // 2012-06-30T23:59:60,000000000Z

  System.out.println(
    "Japan-Time: " +
    Formatters.localizedPattern("yyyy-MM-dd'T'HH:mm:ss")
              .withTimezone(TZID.ASIA.TOKYO)
              .format(leapsecondUTC)); // Japan-Time: 2012-07-01T08:59:60
```

Design remarks:

a) Although Time4J is strongly generified users will not really use any generics in their application code as demonstrated in example code, but are more or less type-safe at compile-time. For example, it is impossible to add clock units to a calendar date.

b) In contrast to most other libraries Time4J does not like implicit defaults. Users have to explicitly specify what locale or time zone they want. And if they want the default then they spell it so in methods like: "inSystemTimezone()" or "localizedPattern(...)". This philosophy is also the reason why the class "PlainDate" is missing a static method like "today()". This method instead exists in the class "ZonalClock" making clear that you cannot achieve the current local date and time without specifying the time zone.

c) Time4J offers a lot of manipulations of date and time by an element-centric approach. Every basic type like "PlainTime" registers some elements (similar to fields in other libraries) which serve as access key to chronological partial data. These elements like "MINUTE_OF_HOUR" offer many different manipulation methods, called operators using the strategy pattern idea. With this design it is possible to manipulate a "PlainTime" in 171 different ways regarding the elements. Another advantage of this design: Despite the size of features the count of methods in most classes is still not too big, "PlainTime" has only 38 methods including the inherited methods from super classes.

d) Another way of manipulation is date/time-arithmetic along a time axis. All four basic types have their time axis. For example roughly spoken "Moment" is defined as an elapsed count of SI-seconds since UTC epoch while a calendar date (here: "PlainDate") maps dates to julian days - another kind of time axis. The essentials of this time arithmetic are realized via the abstract super class "TimePoint". So all four basic types inherit methods like "plus(n, units)", "until(...)" etc for supporting adding, subtracting and evaluating durations.

e) Time4J rejects the design idea of JSR-310 to separate between "machine time" and "human time". This is considered as artificial. So all four basic types offer both aspects in one. For example a calendar date is simultaneously a human time consisting of several meaningful elements like year, month etc. and also a kind of machine or technical time counter because you can define a single incrementing number represented by julian days. As result of this dualism Time4J also does not separate between specialized JSR-310-time spans like "Period" or "Duration" but just has one duration class, namely "PlainDuration", applicable on "PlainDate", "PlainTime" and "PlainTimestamp".

f) In general Time4J does not try to invent things which are not in any real use. For example users will not find era arithmetic like in JSR-310 or an purely academic UTC-SLS implementation. On the other side Time4J does indeed try to model the reality as exact as possible, even the rare feature of leap seconds.

Plans for next release:
-----------------------

There are still many features missing. For the next release especially more time zone support is planned, including the support of CLDR-features like "z" in format patterns for time zone names.

Downloads:
----------

You can find any downloads on the release page.

Tutorials:
----------

English tutorials and code examples will soon be presented on the website "http://www.time4j.net/".

Blog:
-----

If you are capable of German language you can also read my blog "http://www.menodata.de/blog/" where I sometimes post my views about Java and Time, for example my review of JSR-310.
