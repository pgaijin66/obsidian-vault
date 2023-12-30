```

const (
	// 1XXX code are related to files and directory
	ErrFileNotFound   Err = 1001
	ErrPathNotDefined Err = 1002

)

type Err int

func (e Err) Message() string {
	switch e {
	case ErrFileNotFound:
		return "file not found"
	case ErrBadOTP:
		return "bad otp"
	default:
		return ""
	}
}
```