---
id: example
title: Example app
---
This application shows basic capabilities of the HassModel API of NetDaemon. It has a single file `ExampleApp.cs` that contains the app logic. This example uses the code generation capability of HassModel [explained here](v3/hass_model/hass_model_codegen.md).

## ExampleApp.cs

```cs
using System;
using System.Reactive.Linq;
using NetDaemon.AppModel;
using NetDaemon.HassModel;
using HomeAssistantGenerated;

[NetDaemonApp]
public class ExampleAppHaContext
{
    public ExampleAppHaContext(IHaContext ha)
    {
        var entities = new Entities(ha);
        
        entities.BinarySensor.OfficeMotion
            .StateChanges()
            .Where(e => e.New.IsOn())
            .Subscribe(_ =>entities.Light.Office.TurnOn());
    }
}
```

## The NetDaemonAppAttribute

```cs
[NetDaemonApp]
```

By decorating a class with the `NetDaemonAppAttribute` it is registered as an application to be loaded by NetDaemon.

## The constructor
```cs
public ExampleAppHaContext(IHaContext ha)
```

When the application is (re-)started a new instance of the class is created by calling its constructor. This constructor will receive constructor arguments by using the standard .Net dependency injection mechanism. In this example the constructor receives an IHaContext interface which provides basic methods for interacting with Home Assistant.

The constructor can be used to do initialization of your application. **Never block the constructor!** Typically here you configure what should happen when a state changes or run a function every minute for example. If you need to do asynchronous initialization of your application this can be done by implementing `IAsyncInitializable`

**Example:**

```cs
        var entities = new Entities(ha);
        
        entities.BinarySensor.OfficeMotion
            .StateChanges()
            .Where(e => e.New.IsOn())
            .Subscribe(_ => entities.Light.Office.TurnOn());
```

| Function        | Description                                                              |
| --------------- | -------------------------------------------------------------------------|
| new Entities(ha)     | Creates an Instance of the generated Entities class that provides strong typed access to all your HA entities |
| entities.BinarySensor.OfficeMotion          | Selects the `binary_sensor.office_motion`  entity from HomeAssitant |
| StateChanges()  | Respond to changes of the state of this motion sensor
| Where           | FIlter which state changes we are interested in, this case when the sensor' state becomes 'on' |
| Subscribe       | Calls any code/action when criteras met                                  |
| TurnOff()       | Turns off the lights in the office using another generated entity and a generated service method |

## Creating and updating HomeAssistant entities

If you want to create entities from NetDaemon, set their state attributes, then check out the [MQTT Entity Manager documentation](v3/extensions/mqttEntityManager.md)

## Real-world example apps

Please check out the apps being developed for netdaemon. Since documentation is still lacking behind it will be best looking at real code 😊

| User                                                                                                    | Description                                           |
| ------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| [@FrankBakkerNl](https://github.com/FrankBakkerNl/NetDaemonExample)                                    | HassModel examples by Frank the author of HassModel API |
| [@helto4real](https://github.com/helto4real/HassModelAutomations)                          | Tomas netdaemon apps running in production (The HassModel version)          |
| [@isabellaalstrom](https://github.com/isabellaalstrom/home-assistant-config/tree/master/netdaemon/apps) | Isabella's netdaemon apps, check them out, nice stuff |
| [@Horizon0156](https://github.com/Horizon0156/netdaemon-apps)                                           | Stefan W's netdaemon apps, good example extending netdaemon  functionality |
| [@Kennetjuh](https://github.com/kennetjuh/NetDeamonImpl)                                              | Kennetjuh's netdaemon implementation (V3) |
