

Imagine we have a legacy system that logs messages in a certain way and a new system that has a different interface for logging.

The legacy system has a LegacyLogger with a LogMessage method:

```
type LegacyLogger struct {}

func (l *LegacyLogger) LogMessage(message string) {
    fmt.Println("Legacy logger: " + message)
}
```

The new system uses an interface called NewLogger with a Log method:

```
type NewLogger interface {
    Log(message string)
}

type NewSystemLogger struct {}

func (l *NewSystemLogger) Log(message string) {
    fmt.Println("New system logger: " + message)
}
```

You want the new system to be able to use the LegacyLogger without changing its code. You can create an adapter that implements the NewLogger interface, but uses the LegacyLogger internally:

```
type LoggerAdapter struct {
    legacyLogger *LegacyLogger
}

func (a *LoggerAdapter) Log(message string) {
    // Adapting the interface here:
    a.legacyLogger.LogMessage(message)
}
```

Now, you can use LegacyLogger in the new system:

```
func logMessage(message string, logger NewLogger) {
    logger.Log(message)
}

func main() {
    legacyLogger := &LegacyLogger{}
    loggerAdapter := &LoggerAdapter{legacyLogger: legacyLogger}

    logMessage("Hello, world!", loggerAdapter)
}
```

In this example, LoggerAdapter is the adapter. It wraps LegacyLogger (the adaptee) and makes it compatible with NewLogger (the target interface). The Log method in LoggerAdapter adapts the interface of LegacyLogger to the NewLogger interface. The client code (the logMessage function) works with the NewLogger interface, so it can now use LegacyLogger through the LoggerAdapter. This is the essence of the Adapter Pattern.