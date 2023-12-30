

```
type Logger interface {
	Log(message string)
}

type ConsoleLogger struct{}

func (cl *ConsoleLogger) Log(message string) {
	fmt.Println(message)
}

type FileLogger struct {
	file *os.File
}

func (fl *FileLogger) Log(message string) {
	fmt.Fprintln(fl.file, message)
}

type LoggerFactory struct{}

func (lf *LoggerFactory) CreateLogger(loggerType string) (Logger, error) {
	switch loggerType {
	case "console":
		return &ConsoleLogger{}, nil
	case "file":
		file, err := os.Create("log.txt")
		if err != nil {
			return nil, err
		}
		return &FileLogger{file: file}, nil
	default:
		return nil, fmt.Errorf("unsupported logger type")
	}
}
```