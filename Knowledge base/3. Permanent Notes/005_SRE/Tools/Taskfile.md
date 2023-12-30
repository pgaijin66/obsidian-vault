

If you have been using Makefile, you know the hassel around managing it. For smaller projects, it does not create much issues, but as the project grows larger, the issues around management and operation will start to become more evident.

So in this video, i will be showing one of the tool i have been using for sometime for larger projects. 

It is called go-task. It is similar to Makefile but much simpler, manageable and readable.


Task is a build tool which aims to simpler and easier to use than using GNU makefile. 

So let's get right into it. 

We will learn about Taskfile by creating one for our sample project.


### File name
```
touch Taskfile.yml
```


### version
```
version: '3'
```


### define task

```
version: '3'

tasks:
	build:
		cmds:
			- echo "Hello world"
```


## define variable

```
version: '3'

tasks:
	build:
		vars:
			PORT: "9090"
		cmds:
			- echo "Hello world"
			- echo $PORT

```



### Define environment variable

```
version: '3'

tasks:
	build:
		env:
			HOST: "http://localhost"
		cmds:
			- echo $HOST
```



### dotenv

```
version: '3'

dotenv:
	- ".env"

tasks:
	build:
		env:
			DB_PASSWORD: "{{.DB_PASSWORD}}"
		cmds:
			- echo $DB_PASSWORD
```



Global env variable

```
version: '3'

dotenv:
	- ".env"
env:
	APP_NAME: "demo-app"

tasks:
	build:
		env:
			DB_PASSWORD: "{{.DB_PASSWORD}}"
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
```



### Rename task

```
version: '3'


tasks:
	build:
		label: "Build go application"
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
```


### Add description
shown in `--list` or `--list-all`
```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
```


### Add summary

```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		summary: |
			Builds app for project

			It will build the project. Please make sure you have DB_PASSWORD
			set.
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
```

shown in `task --summary build`

```
task --summary build
```


### Define task within a task

```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		summary: |
			Builds app for project

			It will build the project. Please make sure you have DB_PASSWORD
			set.
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
			- task: run-go-audit
	run-go-audit:
		cmd:
			- go audit main.go
```


### Add task aliases
```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		summary: |
			Builds app for project

			It will build the project. Please make sure you have DB_PASSWORD
			set.
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
			- task: audit
	run-go-audit:
		aliases: audit
		cmd:
			- go audit main.go
```


### run task if file changes

```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		summary: |
			Builds app for project

			It will build the project. Please make sure you have DB_PASSWORD
			set.
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
			- task: audit
		sources:
			- main.go

	run-go-audit:
		aliases: audit
		cmd:
			- go audit main.go
```


### Commands to check if a task should run
```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		summary: |
			Builds app for project

			It will build the project. Please make sure you have DB_PASSWORD
			set.
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
			- task: audit
		sources:
			- main.go
		precondition:
			- test -f main.go

	run-go-audit:
		aliases: audit
		cmd:
			- go audit main.go
```


### Using status to override generate, sources, or methods

```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		summary: |
			Builds app for project

			It will build the project. Please make sure you have DB_PASSWORD
			set.
		cmds:
			- echo $DB_PASSWORD
			- echo $APP_NAME
			- task: audit
		sources:
			- main.go
		status:
			- test -f main.go

	run-go-audit:
		aliases: audit
		cmd:
			- go audit main.go
```


### Cleanup using defer

```
version: '3'


tasks:
	build:
		label: "Build go application"
		desc: "Build app and creates an executable binary"
		summary: |
			Builds app for project

			It will build the project. Please make sure you have DB_PASSWORD
			set.
		cmds:
			- defer:
				task: cleanup
			- echo $DB_PASSWORD
			- echo $APP_NAME
			- task: audit
		sources:
			- main.go
		status:
			- test -f main.go

	run-go-audit:
		aliases: audit
		cmd:
			- go audit main.go

	cleanup-workspace:
		aliases: cleanup
		cmd:
			- rm -rf main
```



### Define shell options

```
version: '3'

set: [pipefail]  
shopt: [globstar]

tasks:  
# `globstar` required for double star globs to work  
default: echo **/*.go
```

### Watch tasks

```
task --watch build
```

Watches task for files changes and run the task again

you can define interval as well using 
```
internval: 500ms
```

or

```
task --watch --interval=500ms build
```

### JSON

```
task build --json --list
```

```
# Current version of Taskfile
version: '3'

# global environment variables
env:
  VAR1: "This is a global var"

# Loading values from .env file
dotenv:
  - ".env"

# map[string]tasks
tasks:
  # individual task
  build:
    # Overriding the name of taskfile in output
    label: "Build go app"
    desc: "Builds app and created a executable binary"
    # if summary is missing, description will be printed. If a task does not has a summar or description, a warning is printed
    summary: |
      Release your project to github

      It will build your project before starting the release.
      Please make sure that you have set GITHUB_TOKEN before starting.
    # defining local env variables
    env:
      VAL: "{{.ENV_VALUE}}"
    vars:
      VAR: "hello"
    cmds:
      # Runs cleanup task once this task finishes
      # - defer:
      #   task: cleanup
      # - go build -v -o demo main.go
      - echo '{{.VAR}}''
      # - task: audit
    # Run this task if source files changes
    # sources:
    #   - main.go
    # The result of the command should yield in a binary file named "main"
    # checking if the file is created or not
    # generates:
    #   - ./main
    # # list of commands to cehck if this task should run. If a condition are not met, task will return an error
    # preconditions:
    #   - test -f main.go
    # # list of commands to check if this task should run. It overrides method, sources or generates
    # status:
    #   - test -f main
    # silent: true

  app-audit:
    aliases: [audit]
    cmds:
      - go vet main.go

  cleanup-task:
    aliases: [cleanup]
    cmds:
      - rm -rf demo
```
