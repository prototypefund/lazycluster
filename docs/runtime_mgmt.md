
**Source:** [/lazycluster/runtime_mgmt.py](/lazycluster/runtime_mgmt.py#L0)


-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L15)</span>

## RuntimeGroup class

A group of logically related `Runtimes`.

The `RuntimeGroup` provides a simplified interface for executing tasks in multiple `Runtimes` or exposing ports
within a `RuntimeGroup`.

**Examples:**

  Execute a `RuntimeTask in a `RuntimGroup`
  ```python
  >>> # Create instances
  >>> group = RuntimeGroup([Runtime('host-1'), Runtime('host-2')])
  >>> # group = RuntimeGroup(hosts=['host-1', 'host-2'])
  >>> my_task = RuntimeTask('group-demo').run_command('echo Hello Group!')

  >>> # Execute a RuntimeTask in a single Runtime
  >>> single_task = group.execute_task(my_task)
  >>> print(single_task.execution_log[0])

  >>> # Execute a RuntimeTask in the whole RuntimGroup
  >>> task_list = group.execute_task(my_task, broadcast=True)

  >>> # Execute RuntimeTask via RuntimGroup either on a single Runtime
  >>> my_task = RuntimeTask('group-demo').run_command('echo Hello Group!')
  >>> task = group.execute_task(my_task)
  ```
  A DB is running on localhost on port `local_port` and the DB is only accessible
  from localhost. But you also want to access the service on the other `Runtimes` on port
  `runtime_port`. Then you can use this method to expose the service which is running on the
  local machine to the remote machines.
  ```python
  >>> # Expose a port to all Runtimes contained in the Runtime. If a port list is given the next free port is
  >>> # chosen and returned.
  >>> group_port = group.expose_port_to_runtimes(local_port=60000, runtime_port=list(range(60000,60010)))
  >>> print('Local port 60000 is now exposed to port ' + str(group_port) + ' in the RuntimeGroup!')
  ```
  A DB is running on a remote host on port `runtime_port` and the DB is only accessible from the remote
  machine itself. But you also want to access the service to other `Runtimes` in the group. Then you can use
  this method to expose the service which is running on one `Runtime` to the whole group.
  ```python
  >>> # Expose a port from a Runtime to all other ones in the RuntimeGroup. If a port list is given the next
  >>> # free port is chosen and returned.
  >>> group_port = group.expose_port_from_runtime_to_group(host='host-1', runtime_port=60000,
  ...                                                      group_port=list(range(60000,60010)))
  >>> print('Port 60000 of `host-1` is now exposed to port ' + str(group_port) + ' in the RuntimeGroup!')
  ```
#### RuntimeGroup.function_returns
 
Blocks thread until a `RuntimeTasks` finished executing and gives back the return data of the remotely
executed python functions. The data is returned in the same order as the Tasks were started

**Returns:**

  Generator[object, None, None]: The unpickled return data.

#### RuntimeGroup.hosts
 
Contained hosts in the group.

**Returns:**

  List[str]: List with hosts of all `Runtimes`.

#### RuntimeGroup.runtime_count
 
Get the count of runtimes contained in the group. 

#### RuntimeGroup.runtimes
 
Contained Runtimes in the group.

**Returns:**

  List[Runtime]: List with all `Runtimes`.

#### RuntimeGroup.task_processes
 
Processes from all contained `Runtimes` which were started to execute a `RuntimeTask`.

**Returns:**

  List[Process]: Process list.

-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L62)</span>

### RuntimeGroup.`__init__`

```python
__init__(
    self,
    runtimes:  Union[List[lazycluster.runtimes.Runtime],
    NoneType]  =  None,
    hosts:  Union[List[str],
    NoneType]  =  None
)
```

Initialization method.

**Args:**

 - `runtimes` (Optional[List[Runtime]]):  List of `Runtimes`. If not given, then `hosts` must be supplied.
 - `hosts` (Optional[List[str]]):  List of hosts, which will be used to instantiate `Runtime` objects. If not
  given, then `runtimes` must be supplied.
**Raises:**

 - `ValueError`:  Either `runtimes` or `hosts` must be supplied. Not both or none.
 - `InvalidRuntimeError`:  If a runtime cannot be instantiated via host.


-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L143)</span>

### RuntimeGroup.add_runtime

```python
add_runtime(
    self,
    host:  Union[str,
    NoneType]  =  None,
    runtime:  Union[lazycluster.runtimes.Runtime,
    NoneType]  =  None
)
```

Add a `Runtime` to the group either by host or as a `Runtime` object.

**Args:**

 - `host` (Optional[str]):  The host of the runtime. Defaults to None.
 - `runtime` (Optional[Runtime]):  The `Runtime` object to be added to the group. Defaults to None.

**Raises:**

 - `ValueError`:  If the same host is already contained. Or if both host and runtime is given. We refuse
  the temptation to guess. Also if no argument is supplied.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L483)</span>

### RuntimeGroup.cleanup

```python
cleanup(self)
```

Release all acquired resources and terminate all processes by calling the
cleanup method on all contained `Runtimes`. 
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L414)</span>

### RuntimeGroup.clear_tasks

```python
clear_tasks(self)
```

Clears all internal state related to `RuntimeTasks`. 
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L391)</span>

### RuntimeGroup.contains_runtime

```python
contains_runtime(self, host:  str) -> bool
```

Check if the group contains a `Runtime` identified by host.

**Args:**

 - `host` (str):  The `Runtime` to be looked for.

**Returns:**

 - `bool`:  True if runtime is contained in the group, else False.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L290)</span>

### RuntimeGroup.execute_task

```python
execute_task(
    self,
    task:  lazycluster.runtimes.RuntimeTask,
    host:  Union[str,
    NoneType]  =  None,
    broadcast:  bool  =  False,
    execute_async:  bool  =  True
) -> lazycluster.runtimes.RuntimeTask
```

Execute a `RuntimeTask` in the whole group or in a single `Runtime`. 

**Args:**

 - `task` (RuntimeTask):  The task to be executed.
 - `host` (str):  If task should be executed in ine Runtime. Optionally, the host could be set in order to
  ensure the execution in a specific Runtime. Defaults to None. Consequently, the least busy
  `Runtime` will be chosen.
 - `broadcast` (bool):  True, if the task will be executed on all `Runtimes`. Defaults to False.
 - `execute_async` (bool):  True, if execution will take place async. Defaults to True.

**Returns:**

RuntimeTask or List[RuntimeTask]: Either a single `RuntimeTask` object in case the execution took place
  in a single `Runtime` or a list of `RuntimeTasks` if executed in all.

**Raises:**

 - `ValueError`:  If `host` is given and not contained as `Runtime` in the group.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L228)</span>

### RuntimeGroup.expose_port_from_runtime_to_group

```python
expose_port_from_runtime_to_group(
    self,
    host:  str,
    runtime_port:  int,
    group_port:  Union[int,
    List[int],
    NoneType]  =  None
) -> int
```

Expose a port from a `Runtime` to all other `Runtimes` in the `RuntimeGroup` so that all traffic to the
`group_port` is forwarded to the `runtime_port` of the runtime.

**Args:  **

 - `host` (str):  The host of the `Runtime`.
 - `runtime_port` (int):  The port on the runtime.
 - `group_port` (Union[int, List[int], None]):  The port on the other runtimes where the `runtime_port` shall be
  exposed to. May raise PortInUseError if a single port is given.
  If a list is used to automatically find a free port then a
  NoPortsLeftError may be raised. Defaults to runtime_port.

**Returns:**

 - `int`:  The `group_port` that was eventually used.

**Raises:**

 - `ValueError`:  If host is not contained.
 - `PortInUseError`:  If `group_port` is occupied on the local machine.
 - `NoPortsLeftError`:  If `group_ports` was given and none of the ports was free.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L180)</span>

### RuntimeGroup.expose_port_to_runtimes

```python
expose_port_to_runtimes(
    self,
    local_port:  int,
    runtime_port:  Union[int,
    List[int],
    NoneType]  =  None,
    exclude_hosts:  Union[List[str],
    NoneType]  =  None
) -> int
```

Expose a port from localhost to all Runtimes beside the excluded ones so that all traffic on the
`runtime_port` is forwarded to the `local_port` on the local machine. This corresponds to remote
port forwarding in ssh tunneling terms. Additionally, all relevant runtimes will be checked if the port is
actually free.

**Args:**

 - `local_port` (int):  The port on the local machine.
 - `runtime_port` (Union[int, List[int], None]):  The port on the runtimes where the `local_port` shall be
  exposed to. May raise PortInUseError if a single port is given.
  If a list is used to automatically find a free port then a
  NoPortsLeftError may be raised. Defaults to `local_port`.
 - `exclude_hosts` (Optional[List[str]]):  List with hosts where the port should not be exposed to. Defaults to
  None. Consequently, all `Runtimes` will be considered.

**Returns:**

 - `int`:  The port which was actually exposed to the `Runtimes`.

**Raises:**

 - `PortInUseError`:  If `runtime_port` is already in use on at least one Runtime.
 - `ValueError`:  Only hosts or `exclude_hosts` must be provided or host is not contained in the group.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L337)</span>

### RuntimeGroup.get_free_port

```python
get_free_port(
    self,
    ports:  List[int],
    enforce_check_on_localhost:  bool  =  False
) -> int
```

Return the first port from the list which is currently not in use in the whole group.

**Args:**

 - `ports` (List[int]):  The list of ports that will be used to find a free port in the group.
 - `enforce_check_on_localhost` (bool):  If true the port check will be executed on localhost as well, although
  localhost might not be a `Runtime` instance contained in the
  `RuntimeGroup`.

**Returns:**

 - `int`:  The first port from the list which is not yet used within the whole group.

**Raises:**

 - `NoPortsLeftError`:  If the port list is empty and no free port was found yet.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L423)</span>

### RuntimeGroup.get_runtime

```python
get_runtime(
    self,
    host:  Union[str,
    NoneType]  =  None
) -> lazycluster.runtimes.Runtime
```

Returns a runtime based on the host.

**Args:**

 - `host` (str):  The host which identifies the runtime.

**Returns:**

 - `Runtime`:  Runtime objects identified by `host`. Defaults to the least busy one, i.e. the one with the fewest
alive processes that execute a `RuntimeTask`.

**Raises:**

 - `ValueError`:  Hostname is not contained in the group.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L443)</span>

### RuntimeGroup.get_runtimes

```python
get_runtimes(
    self,
    include_hosts:  Union[str,
    List[str],
    NoneType]  =  None,
    exclude_hosts:  Union[str,
    List[str],
    NoneType]  =  None
) -> Dict[str, lazycluster.runtimes.Runtime]
```

Convenient methods for getting relevant `Runtimes` contained in the group.

**Args:**

 - `include_hosts`:  (Union[str, List[str], None] = None): If supplied, only the specified `Runtimes` will be
  returned. Defaults to None, i.e. not restricted.
 - `exclude_hosts`:  (Union[str, List[str], None] = None): If supplied, all `Runtimes` beside the here specified
  ones will be returned. Defaults to an empty list, i.e.
  not restricted.
**Raises:**

 - `ValueError`:  If include_hosts and exclude_hosts is provided or if a host from `include_host` is not contained
  in the group.
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L366)</span>

### RuntimeGroup.has_free_port

```python
has_free_port(
    self,
    port:  int,
    exclude_hosts:  Union[List[str],
    str,
    NoneType]  =  None
) -> bool
```

Check if a given port is free on `Runtimes` contained in the group. The check can be restricted to a
specific subset of contained `Runtimes` by excluding some hosts.

**Args:**

 - `port` (int):  The port to be checked in the group.
 - `exclude_hosts`:  (Union[List[str], str, None]): If supplied, the check will be omitted in these `Runtimes`.
  Defaults to None, i.e. not restricted.

**Returns:**

 - `bool`:  True if port is free on all `Runtimes`, else False.

**Raises:**

 - `ValueError`:  Only hosts or excl_hostnames must be provided or Hostname is
  not contained in the group.                     
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L327)</span>

### RuntimeGroup.join

```python
join(self)
```

Blocks until `RuntimeTasks` which were started via the `runtime.execute_task()` method terminated. 
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L132)</span>

### RuntimeGroup.print_hosts

```python
print_hosts(self)
```

Print the hosts of the group. 
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L332)</span>

### RuntimeGroup.print_log

```python
print_log(self)
```

Print the execution logs of each `RuntimeTask` that were executed in the group. 
-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L169)</span>

### RuntimeGroup.remove_runtime

```python
remove_runtime(self, host:  str)
```

Remove a runtime from the group by host.

**Args:**

 - `host` (str):  The host of the `Runtime` to be removed from the group.

-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L501)</span>

## RuntimeManager class

The `RuntimeManager` can be used for a simplified resource management, since it aims to automatically detect
valid `Runtimes` based on the ssh configuration. It can be used to create a `RuntimeGroup` based on the
automatically detected instances and possibly based on further filters such as GPU availability.

-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L507)</span>

### RuntimeManager.`__init__`

```python
__init__(self)
```

Initialization method.

**Raises:**

 - `NoRuntimeDetectedError`:  If no `Runtime` could be automatically detected.


-------------------
<span style="float:right;">[[source]](/lazycluster/runtime_mgmt.py#L545)</span>

### RuntimeManager.create_group

```python
create_group(
    self,
    include_hosts:  Union[str,
    List[str],
    NoneType]  =  None,
    exclude_hosts:  Union[str,
    List[str],
    NoneType]  =  None,
    gpu_required:  bool  =  False,
    min_memory:  Union[int,
    NoneType]  =  None,
    min_cpu_cores:  Union[int,
    NoneType]  =  None,
    installed_executables:  Union[str,
    List[str],
    NoneType]  =  None,
    filter_commands:  Union[str,
    List[str],
    NoneType]  =  None,
    working_dir:  Union[str,
    NoneType]  =  None
) -> lazycluster.runtime_mgmt.RuntimeGroup
```

Create a runtime group with either all detected `Runtimes` or with a subset thereof.

**Args:**

 - `include_hosts` (Union[str, List[str], None]):  Only these hosts will be included in the `RuntimeGroup`.
  Defaults to None, i.e. not restricted.
 - `exclude_hosts`:  (Optional[List[str]] = None): If supplied, all detected `Runtimes` beside the here specified
  ones will be included in the group. Defaults to None, i.e. not
  restricted.
 - `gpu_required` (bool):  True, if gpu availability is required. Defaults to False.
 - `min_memory` (Optional[int]):  The minimal amount of memory in MB. Defaults to None, i.e. not restricted.
 - `min_cpu_cores` (Optional[int]):  The minimum number of cpu cores required. Defaults to None, i.e. not
  restricted.
 - `installed_executables` (Union[str, List[str], None]):  Possibility to only include `Runtimes` that have an
  specific executables installed. See examples.
 - `filter_commands` (Union[str, List[str], None]):  Shell commands that can be used for generic filtering.
 - `working_dir` (Optional[str]):  The directory which shall act as working one. Defaults to None.
  Consequently, a temporary directory will be created and used as working directory. If
  the working directory is a temporary one it will be cleaned up either `atexit` or
  when calling `cleanup()` manually.

**Note:**

The filter criteria are passed through the `check_filter()` method of the `Runtime` class. See its
documentation for further details and examples.

**Returns:**

 - `RuntimeGroup`:  The created `RuntimeGroup`.

**Raises:**

 - `ValueError`:  Only hosts or excluded_hosts must be provided or Hostname is not contained in the group.
 - `NoRuntimesError`:  If no `Runtime` matches the filter criteria.

