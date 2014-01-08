# Windows service

## Topshelf

[http://topshelf-project.com/](http://topshelf-project.com/)

### Installation

consumer.exe -instance:1 -username:username -password:password

### Adjust log folder names

```csharp
private void AdjustLogWithInstanceName(string instanceName)
{
    // if only one instance on local, use the default namespace log folder
    string defaultLogFolderName = GetType().Namespace;
    if (string.IsNullOrEmpty(instanceName) || instanceName.ToLower() == defaultLogFolderName.ToLower())
        return;

    var appenders = LogManager.GetRepository().GetAppenders();
    var fileAppenders = appenders.Where(f => f.GetType() == typeof(RollingFileAppender));
    foreach (FileAppender appender in fileAppenders)
    {
        appender.File = appender.File.Replace(defaultLogFolderName, defaultLogFolderName + "_" + ServiceBootstrapper.InstanceName);
        appender.ActivateOptions();
    }
    var emailAppender = appenders.FirstOrDefault(c => c.GetType() == typeof(Logging.SmtpCachingAppender));
    if (emailAppender != null)
    {
        var appender = (Logging.SmtpCachingAppender)emailAppender;
        appender.Subject = appender.Subject.Replace(defaultLogFolderName, defaultLogFolderName + "_" + ServiceBootstrapper.InstanceName);
        appender.ActivateOptions();
    }
}

```