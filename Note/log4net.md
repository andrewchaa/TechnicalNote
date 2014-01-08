# log4net

## Configuration

### Config file

```xml
<?xml version="1.0"?>
<log4net>
  <appender name="ErrorRollingFile" type="log4net.Appender.RollingFileAppender">
    <threshold value="WARN" />
    <file value="D:\dev\huddle\log\Huddle.Identity.EventConsumer\EventConsumer-error.log" />
    <lockingModel type="log4net.Appender.FileAppender+MinimalLock" />
    <appendToFile value="true" />
    <rollingStyle value="Date" />
    <datePattern value="yyyyMMdd" />
    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="%utcdate [%thread] %-5level %logger - %message%n" />
    </layout>
  </appender>

  <appender name="Console" type="log4net.Appender.ColoredConsoleAppender">

    <mapping>
      <level value="ERROR" />
      <foreColor value="RED, HighIntensity" />
    </mapping>

    <mapping>
      <level value="DEBUG" />
      <foreColor value="White" />
    </mapping>

    <mapping>
      <level value="INFO" />
      <foreColor value="Cyan" />
    </mapping>

    <mapping>
      <level value="WARN" />
      <foreColor value="Yellow, HighIntensity" />
    </mapping>

    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="%utcdate [%thread] %-5level %logger - %message%n" />
    </layout>
  </appender>

  <root>
    <appender-ref ref="EventLogs" />
    <appender-ref ref="Console" />
    <appender-ref ref="RollingFile" />
    <appender-ref ref="ErrorRollingFile" />
    <appender-ref ref="RollUpErrorEmail" />
    <appender-ref ref="UdpAppender" />
    <level value="DEBUG" />
  </root>

</log4net>
```