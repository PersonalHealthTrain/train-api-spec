# The Train API V1

## Introduction

The Train API defines the set of commands that a train image needs to understand
when a container is created. Each command is characterized by the following
attributes:
* The behavior that the started container will exhibit
* The [OCI runtime config](https://github.com/opencontainers/image-spec/blob/master/config.md)
  options that will alter the behavior
* Additional command-line options specific to the command
* The Output JSON Schema of the command, which is output as a suffix
  of the containers stdout. The output of a running train container in
  this format is called the train response.

Each train image needs to implement the Train API to be valid.

## Train API Specification

### Commands
The Train API V1 supports the following behaviors and commands.

Behavior                        | Command               | Description | OCI Image Config at runtime
--------------------------------|-----------------------|---------------------------------------
Print a summary of the model    | `print_model_summary` | Print the summary of the model as the train response. This is intended to give the command issuer a brief summary of the state that the model is currently in (like what are the weight s currently) | None
Runs the encapsulated algorithm | `run_algorithm`       | Runs the encapsulated algorithm. The resulting exited container contains files that is used to generate the successor train image. | **config.Env**
List all the resources that the train wants to consume | `list_resources` | Lists all the resources that the train wants to consume. This interface can be used to determine the requirements of a train at a very explicit level. | None
Checks whether all requirements are fullfilled such that the train can run | `check_requirements` | The train is supposed to perform a very quick check whether all requirements are met such that the algorithm can run at all.  This might for instance include checking of present environment variables | **config.Env**  

### Train responses
Here we list the schema and an example response of each train command.

#### print_model_summary
**Schema**
```
{
   "definitions": {},
   "$schema": "http://json-schema.org/draft-07/schema#",
   "$id": "print_model_summary.json",
   "type": "object",
   "title": "Schema for the print_model_summary train response",
   "required": [
     "content"
   ],
   "properties": {
     "content": {
       "$id": "#/properties/content",
       "type": "string",
       "title": "The Content Schema",
       "default": "",
       "examples": [
         "This is some String describing the summary of the model computed so far."
        ],
       "pattern": "^(.*)$"
      }
   }
}
```
**Example**
```
{
  "content": "This is an example summary of a statistical model"
}
```

#### run_algorithm
**Schema**





## Specific command line interfaces

### Docker
Executing the `docker run` (on version 18.09.0-ce) results in the following
output:
```
"docker run" requires at least 1 argument.
See 'docker run --help'.

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container
```
The Train API specifies the set of commands that can be passed to `[COMMAND]`
and the behavior that the started container will then exhibit.

### rkt
Executing `rkt run --help` shows us the following usage:
```
USAGE:
	rkt run [--volume=name,kind=host,...] [--mount volume=VOL,target=PATH] IMAGE [-- image-args...[---]]...
```
Here, the Train API specifies the possible commands that can be passed
to `image-args`.




## API Implementation
### Python