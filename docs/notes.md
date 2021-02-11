# Learning Bolt
Source: https://puppet.com/docs/bolt/latest/getting_started_with_bolt.html

## Installation instructions
https://puppet.com/docs/bolt/latest/bolt_installing.html

## Bolt project directory
A bolt project is a directory that contains a `bolt-project.yaml` file. To initialize the Bolt project directory with this file, run the following command:

```shell
bolt project init my_project_name 
```

In order for Bolt to use plans, this directory needs to adhere a specific folder structure, for example:

- `[Bolt project root]/modules/mongodb/plans`
- `[Bolt project root]/modules/mongodb/files`

## Running a command on a remote machine with bolt
To run a command on a remote machine we use the `bolt command run` command

```shell
bolt command run whoami -t 127.0.0.1:2000 -u root -p root --no-host-key-check
```

Output:
```
  STDOUT:
    root
Successful on 1 target: 127.0.0.1
Ran on 1 target in 0.35 sec
```

The -t parameter defines the target machine to run the command on, the -u option the user to login with and the -p option the password.
