
```
// using SendGrid's Go Library
// https://github.com/sendgrid/sendgrid-go
package email

import (
	"fmt"
	"os"

	"github.com/sendgrid/sendgrid-go"
	"github.com/sendgrid/sendgrid-go/helpers/mail"
)

// Email represents the required data for sending email
type Email struct {
	SenderName    string
	SenderEmail   string
	ReceiverName  string
	ReceiverEmail string
	Subject       string
	Body          string
	BodyPlainText string
}

// SendEmail sends a new email. It returns a status code with error
func (r *Email) SendEmail() (int, error) {
	sender := mail.NewEmail(r.SenderName, r.SenderEmail)
	to := mail.NewEmail(r.ReceiverName, r.ReceiverEmail)
	message := mail.NewSingleEmail(sender, r.Subject, to, r.BodyPlainText, r.Body)
	client := sendgrid.NewSendClient(os.Getenv("SENDGRID_API_KEY"))

	response, err := client.Send(message)
	if err != nil {
		return 0, fmt.Errorf("error sending email via twillio sendgrid: %v", err)
	}

	return response.StatusCode, nil
}
```