# store forward cubesat

## scheduling

- non-preemptive round robin. ex: superloop that runs released tasks in a fixed order.

## scope

- a sporadic task does one step per release.
- `timer_1hz_task` releases a sporadic task again while its work remains.

## isr

| isr | released by | work |
| --- | --- | --- |
| `timer_1hz_isr` | timer | release `timer_1hz_task`. |
| `uart_rx_isr` | uart rx | transfer rx data. |
| `uart_tx_isr` | uart tx, `timer_1hz_task`, `flash_task` | send tx data. |
| `fault_isr` | edac, power monitor | transfer fault. |

## task table

| task | kind | device wait | deadline | order | work |
| --- | --- | --- | --- | --- | --- |
| `timer_1hz_task` | periodic | no | 1s | 1 | command and telemetry, mode, pass schedule, store and forward control |
| `flash_task` | sporadic | no | 10s | 2 | message store step, message read step |

## matrix

| sender \ receiver | `uart_tx_isr` | `timer_1hz_task` | `flash_task` |
| --- | --- | --- | --- |
| `timer_1hz_isr` | - | R | - |
| `uart_rx_isr` | - | D rx | - |
| `fault_isr` | - | D fault | - |
| `timer_1hz_task` | R D telemetry | - | R D message and request |
| `flash_task` | R D forward message | D flash progress | - |

## device table

| device | accessor |
| --- | --- |
| timer 1hz channel | `timer_1hz_isr` |
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
| 6 | not applied |
| 7 | pass |
