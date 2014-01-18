# NHibernate

## logging

### [Disable NHibernate logging](http://stackoverflow.com/questions/1467776/disable-nhibernate-logging)

Configure Log4Net for use with NHibernate

In order to turn off NHibernate debug log

```xml
 <logger name="NHibernate">
   <level value="ERROR" />
 </logger>

 <logger name="NHibernate.SQL">
   <level value="ERROR" />
 </logger>
```

If you want to have a separate log file

```xml
  <appender name="ErrorNHibernateLog" type="log4net.Appender.RollingFileAppender">
    <file value="D:\dev\huddle\log\Huddle.Login.Api\api-nhibernate.log" />
    <appendToFile value="true" />
    <rollingStyle value="Date" />
    <datePattern value="yyyyMMdd" />
    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="%utcdate [%thread] %-5level %logger user=%property{user} - %message%n" />
    </layout>
  </appender>

  <logger name="NHibernate"><appender-ref ref="ErrorNHibernateLog" /><level value="WARN" /></logger>
  <logger name="NHibernate.SQL"><appender-ref ref="ErrorNHibernateLog" /><level value="WARN" /></logger>
```