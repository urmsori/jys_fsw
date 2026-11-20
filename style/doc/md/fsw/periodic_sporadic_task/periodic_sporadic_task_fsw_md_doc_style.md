# periodic sporadic task

## purpose

- define the execution context view of fsw architecture.
- divide tasks into periodic task and sporadic task, and define their relation.

## scope

- apply to rtos and baremetal.
- terms follow standard rtos and real-time scheduling terms.

## scheduling

| scheduling | preemption | priority | example |
| --- | --- | --- | --- |
| preemptive fixed priority | yes | yes | rtos, task as software interrupt |
| non-preemptive fixed priority | no | yes | superloop that runs the highest released task |
| non-preemptive round robin | no | no | superloop that runs released tasks in a fixed order |

- isr has higher priority than any task.

## task

| task | released by | device wait | name |
| --- | --- | --- | --- |
| periodic task | timer isr | no | `timer_{rate}_task` |
| sporadic task | periodic task | yes if preemption | `{thing}_task` |

- a task has one release source.
- a release during run is merged into one.
- if scheduling has priority, priority is deadline monotonic.
- if scheduling has no priority, order is fixed.
- rate is frequency. ex: `128hz`, `1hz`.
- if a rate is used more than once, add offset after rate. ex: `timer_8hz_50ms_task`.
- thing is a noun.

## isr

| isr | released by | work | name |
| --- | --- | --- | --- |
| timer isr | timer | release periodic task. | `timer_{rate}_isr` |
| peripheral isr | device, task | exchange data with device. | `{source}_isr` |

- isr does only the work in the table.
- timers linked by offset come from one clock.

## relation

- context is isr or task.
- device is the smallest hw unit that can be accessed separately.
- wait is to stop until another context acts.
- a context does not wait for another context.
- a context does not stop another context.

| relation | gives |
| --- | --- |
| data transfer | data |
| device access | device use |

- data transfer does not release.
- one data has one writer context.
- one device has one accessor. accessor is one context or hw.
- other contexts transfer data to the accessor context.

## matrix

- row is sender context. column is receiver context.
- omit empty rows and columns.
- symbol: `R` release, `D` data transfer, `-` forbidden.
- write data name in `D` cell.

| sender \ receiver | peripheral isr | periodic task | sporadic task |
| --- | --- | --- | --- |
| timer isr | - | R | - |
| peripheral isr | - | D | D |
| periodic task | R D | D | R D |
| sporadic task | R D | D | D |

- write the scheduling.
- task table has kind, device wait, deadline, and priority or order of each task.
- device table has accessor of each device.

### check

| no | where | rule | basis |
| --- | --- | --- | --- |
| 1 | task column of matrix | exactly one `R`. | [task](#task) |
| 2 | peripheral isr row of matrix | no `R`. | [isr](#isr) |
| 3 | column of matrix with `D` | has `R`, or hw drives it. | [relation](#relation) |
| 4 | `D` cells of matrix | a data name is in one row only. | [relation](#relation) |
| 5 | device table | exactly one accessor. | [relation](#relation) |
| 6 | task table, if scheduling has priority | higher priority has shorter or equal deadline. | [task](#task) |
| 7 | task table | device wait is yes only for sporadic task with preemption. | [task](#task) |

## end

- this rule is kept when all checks pass.
