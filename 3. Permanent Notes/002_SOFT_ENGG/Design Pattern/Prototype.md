
```
// RegistrationForm is a concrete type implementing Form
type RegistrationForm struct {
	Name  string
	Email string
}

// Clone method for RegistrationForm
func (r *RegistrationForm) Clone() Form {
	return &RegistrationForm{
		Name:  r.Name,
		Email: r.Email,
	}
}
```