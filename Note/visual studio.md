# Visual Studio

## Pre / Post - build events

To stop and start a windows service on build

[Use Build Events to rebuild a Windows Service without having to manually stop/start it](http://weblogs.asp.net/joelvarty/archive/2008/11/26/use-build-events-to-rebuild-a-windows-service-without-having-to-manually-stop-start-it.aspx)

Pre-build event

```shell
if $(ConfigurationName) == Debug (
	net stop Identity.EventConsumer
	Exit /b 0
)
```

Sometimes, the service hasn't started, so put Exit /b 0

Post-build event

```shell
if $(ConfigurationName) == Debug (
	net start Identity.EventConsumer
)
```