


## Example

Let's consider a scenario where we have a Job Posting site and users can subscribe to be notified when a new job is posted. This is a good example for the Observer pattern as it involves a subject (Job Postings) and observers (Subscribed Users) where observers need to be notified when there's a change in the subject.

```
// JobPost is a struct that represents a job posting
type JobPost struct {
	title string
}

// Observer interface
type Observer interface {
	Notify(jobPost JobPost)
}

// JobSeeker implements Observer
type JobSeeker struct {
	name string
}

func (js *JobSeeker) Notify(jobPost JobPost) {
	fmt.Printf("Hi %s! New job posted: %s\n", js.name, jobPost.title)
}

// JobBoard is our subject struct
type JobBoard struct {
	subscribers []Observer
}

func (jb *JobBoard) Subscribe(observer Observer) {
	jb.subscribers = append(jb.subscribers, observer)
}

func (jb *JobBoard) AddJob(jobPost JobPost) {
	for _, subscriber := range jb.subscribers {
		subscriber.Notify(jobPost)
	}
}
```