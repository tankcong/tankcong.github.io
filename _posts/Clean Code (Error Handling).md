# Clean Code (Error Handling)

## Use Exceptions Rather Than Return Codes
Error Codes:
```
public class DeviceController {
  public void sendShutDown() {
    DeviceHandle handle = getHandle(DEV1);
    // Check the state of the device
    if (handle != DeviceHandle.INVALID) {
      // Save the device status to the record field
      retrieveDeviceRecord(handle);
      // If not suspended, shut down
      if (record.getStatus() != DEVICE_SUSPENDED) {
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
      } else {
        logger.log("Device suspended. Unable to shut down");
      }
    } else {
      logger.log("Invalid handle for: " + DEV1.toString());
    }
  }
  ...
}
```

Exceptions:
```
public class DeviceController {
  ...

  public void sendShutDown() {
    try {
      tryToShutDown();
    } catch (DeviceShutDownError e) {
      logger.log(e);
    }
  }
  private void tryToShutDown() throws DeviceShutDownError {
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);

    pauseDevice(handle);
    clearDeviceWorkQueue(handle);
    closeDevice(handle);
  }

  private DeviceHandle getHandle(DeviceID id) {
    ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
  }

  ...
}
```
## Use Unchecked Exceptions
Unchecked Exceptions可以理解为Runtime Exception，无需显式捕获。  
Checked Exception的缺点在于它**违背了开闭原则**。

> 如果你在方法中抛出可控异常，而catch语句在几个层级之上，**你就得再catch语句和抛出
> 异常处之间的每个方法签名中声明该异常。**这意味着对软件中较低层级的修改，都将波及
> 更高层级的签名。

其实关于Checked, Unchecked Exception的讨论一直存在，我看到个比较好的评论：

> However, I think checked exceptions are useful - they are used when you want to force the user of your API to think how to 
> handle the exceptional situation (if it is recoverable). It's just that checked exceptions are overused in the Java platform,
> which makes people hate them.

具体可以参看这个StackOverFlow上的讨论,[Java: checked vs unchecked exception explanation](http://stackoverflow.com/questions/6115896/java-checked-vs-unchecked-exception-explanation)

## Define Exception Classes in Terms of a Caller’s Needs

对错误的分类可以依据来源、类型(设备、网络、编程)等。但最重要的是考虑**它们如何被捕获**。
打包第三方API的技巧：
```
    ACMEPort port = new ACMEPort(12);

    try {
      port.open();
    } catch (DeviceResponseException e) {
      reportPortError(e);
      logger.log("Device response exception", e);
    } catch (ATM1212UnlockedException e) {
      reportPortError(e);
      logger.log("Unlock exception", e);
    } catch (GMXError e) {
      reportPortError(e);
      logger.log("Device response exception");
    } finally {
      ...
    }
```
打包为：
```
public class LocalPort {
  private ACMEPort innerPort;

  public LocalPort(int portNumber) {
    innerPort = new ACMEPort(portNumber);
  }

  public void open() {
    try {
      innerPort.open();
    } catch (DeviceResponseException e) {
      throw new PortDeviceFailure(e);
    } catch (ATM1212UnlockedException e) {
      throw new PortDeviceFailure(e);
    } catch (GMXError e) {
      throw new PortDeviceFailure(e);
    }
  }
  ...
}
```
最终的调用像这样：
```
    LocalPort port = new LocalPort(12);
    try {
      port.open();
    } catch (PortDeviceFailure e) {
      reportError(e);
      logger.log(e.getMessage(), e);
    } finally {
      ...
    }
```
这样的好处显而易见，降低了对某特定API的依赖，可以相对简单的替换第三方API。

## Do NOT Return Null
## Do NOT Pass Null
