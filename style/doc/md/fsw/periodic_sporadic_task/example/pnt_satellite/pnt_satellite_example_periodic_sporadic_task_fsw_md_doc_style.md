# pnt satellite

## scheduling

- preemptive fixed priority. ex: rtos.

## isr

| isr | released by | work |
| --- | --- | --- |
| `timer_10hz_isr` | timer | release `timer_10hz_task`. |
| `timer_1hz_isr` | timer | release `timer_1hz_task`. |
| `sensor_isr` | sensor bus, `timer_10hz_task` | send sensor request, transfer response. |
| `uart_rx_isr` | uart rx | transfer rx data. |
| `uart_tx_isr` | uart tx, `timer_1hz_task`, `flash_task` | send tx data. |
| `fault_isr` | edac, power monitor | transfer fault. |

## task table

| task | kind | device wait | deadline | priority | work |
| --- | --- | --- | --- | --- | --- |
| `timer_10hz_task` | periodic | no | 100ms | 1 | attitude control |
| `timer_1hz_task` | periodic | no | 1s | 2 | clock, integrity, navigation message, mode, command and telemetry |
| `orbit_task` | sporadic | no | 10s | 3 | orbit determination and prediction, ephemeris |
| `flash_task` | sporadic | yes | 60s | 4 | log store, dump read, upload file store |

## matrix

| sender \ receiver | `sensor_isr` | `uart_tx_isr` | `timer_10hz_task` | `timer_1hz_task` | `orbit_task` | `flash_task` |
| --- | --- | --- | --- | --- | --- | --- |
| `timer_10hz_isr` | - | - | R | - | - | - |
| `timer_1hz_isr` | - | - | - | R | - | - |
| `sensor_isr` | - | - | D sensor | - | - | - |
| `uart_rx_isr` | - | - | - | D rx | - | - |
| `fault_isr` | - | - | - | D fault | - | - |
| `timer_10hz_task` | R D request | - | - | D attitude | - | D attitude log |
| `timer_1hz_task` | - | R D telemetry | - | - | R D measurement | R D log and request |
| `orbit_task` | - | - | D orbit | D ephemeris | - | D orbit log |
| `flash_task` | - | R D dump | - | - | - | - |

## device table

| device | accessor |
| --- | --- |
| timer 10hz channel | `timer_10hz_isr` |
| timer 1hz channel | `timer_1hz_isr` |
| sensor bus | `sensor_isr` |
| actuator bus | `timer_10hz_task` |
| uart rx | `uart_rx_isr` |
| uart tx | `uart_tx_isr` |
| edac, power monitor | `fault_isr` |
| navigation signal block | `timer_1hz_task` |
| optical terminal control port | `timer_1hz_task` |
| thruster | `timer_1hz_task` |
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
