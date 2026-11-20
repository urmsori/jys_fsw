# imaging cubesat

## scheduling

- non-preemptive fixed priority. ex: superloop that runs the highest released task.

## scope

- image data goes from camera to payload memory to downlink without obc context.
- so image data is not in the matrix.
- a sporadic task does one step per release.
- `timer_1hz_task` releases a sporadic task again while its work remains.

## isr

| isr | released by | work |
| --- | --- | --- |
| `timer_10hz_isr` | timer | release `timer_10hz_task`. |
| `timer_1hz_isr` | timer | release `timer_1hz_task`. |
| `sensor_isr` | sensor bus, `timer_10hz_task` | send sensor request, transfer response. |
| `payload_isr` | payload bus, `timer_1hz_task` | send payload command, transfer payload status. |
| `uart_rx_isr` | uart rx | transfer rx data. |
| `uart_tx_isr` | uart tx, `timer_1hz_task`, `flash_task` | send tx data. |
| `fault_isr` | edac, power monitor | transfer fault. |

## task table

| task | kind | device wait | deadline | priority | work |
| --- | --- | --- | --- | --- | --- |
| `timer_10hz_task` | periodic | no | 100ms | 1 | attitude control, target pointing |
| `timer_1hz_task` | periodic | no | 1s | 2 | command and telemetry, mode, imaging schedule, power |
| `orbit_task` | sporadic | no | 60s | 3 | orbit propagation step |
| `flash_task` | sporadic | no | 60s | 4 | log store step, dump read step |

## matrix

| sender \ receiver | `sensor_isr` | `payload_isr` | `uart_tx_isr` | `timer_10hz_task` | `timer_1hz_task` | `orbit_task` | `flash_task` |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `timer_10hz_isr` | - | - | - | R | - | - | - |
| `timer_1hz_isr` | - | - | - | - | R | - | - |
| `sensor_isr` | - | - | - | D sensor | - | - | - |
| `payload_isr` | - | - | - | - | D payload status | - | - |
| `uart_rx_isr` | - | - | - | - | D rx | - | - |
| `fault_isr` | - | - | - | - | D fault | - | - |
| `timer_10hz_task` | R D request | - | - | - | D attitude | - | D attitude log |
| `timer_1hz_task` | - | R D payload command | R D telemetry | D target | - | R D orbit element | R D log and request |
| `orbit_task` | - | - | - | D orbit | D orbit progress | - | - |
| `flash_task` | - | - | R D dump | - | D flash progress | - | - |

## device table

| device | accessor |
| --- | --- |
| timer 10hz channel | `timer_10hz_isr` |
| timer 1hz channel | `timer_1hz_isr` |
| sensor bus | `sensor_isr` |
| actuator bus | `timer_10hz_task` |
| payload bus | `payload_isr` |
| uart rx | `uart_rx_isr` |
| uart tx | `uart_tx_isr` |
| edac, power monitor | `fault_isr` |
| power switch | `timer_1hz_task` |
| flash | `flash_task` |

## check

| no | result |
| --- | --- |
| 1 | pass |
| 2 | pass |
| 3 | pass |
| 4 | pass |
| 5 | pass |
| 6 | pass |
| 7 | pass |
