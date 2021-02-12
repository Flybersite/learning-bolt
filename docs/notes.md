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
The --no-host-key-check option is added to turn of certificate authentication

:bulb: *You can run this command on multiple machines by specifying a comma seperated list with the -t option*

## Inventory file
By creating an `inventory.yaml` in your project directory, you can create an inventory of all the nodes you want to interact with and provide authentication and node details.

Example inventory file:
```yaml
groups:
  - name: dns-servers
    targets:
      - name: dns1
        uri: 192.168.0.20
      - name: dns2
        uri: 192.168.0.21
    config:
      transport: ssh
      ssh:
        user: root
        password: root
        host-key-check: false
```

Then if you want to run a command on an individual machine you specify the -t option with the name of the target. 
```
bolt command run ... -t dns1
```

If you want to run this command on all the nodes defined in the targets section you can use the command

```
bolt command run ... -t all
```

## Bolt plans
A bolt plan is a file that ties commands, scripts and tasks together so you can create workflows. These plans can be either written in YAML or the Puppet language.

A plan needs to be created in `[Bolt project root]/modules/[module-name]/plans/install.yaml` 

The first part of the `install.yaml` file, you can define parameters that can be passed to the plan. In our example we have the following:

```yaml
parameters:
  targets:
    type: TargetSpec
```

In this parameters block, we pass one parameter called targets with the type TargetSpec. This parameter is defined so that when we run the plan, a list of targets can be passed with the -t option (as shown before).

Next up we have our step blocks which outlines the workflow the Bolt plan will follow:

```yaml
steps:
  - name: install_mongodb
    task: package
    targets: $targets
    parameters:
      action: install
      name: mongodb
    description: "Install mongodb"
```

For each step you want to take you need to define a step. In this example we make use of the package task which installs a package on target. The `targets` parameter defines on which nodes it needs to be run.
The `parameters` block define the parameters that are passed to the `package` task. You can define a description as well to describe what the step is doing

## Running a script
You can store your scripts in in `[Bolt project root]/[module-name]/files` and then refer them in your YAML by adding a script step.

Say that we have a script named run_hello.sh in our mongodb module, we can refer to it by using the name of the module and then the name of the script. (We don't need to specify the absolute path to the files directory)

```yaml
steps:
  - name: run_script
    script: mongodb/run_hello.sh'
    targets: $targets
```
