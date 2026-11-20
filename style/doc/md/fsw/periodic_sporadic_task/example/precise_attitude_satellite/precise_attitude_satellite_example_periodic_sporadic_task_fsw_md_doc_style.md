# precise attitude satellite

## scheduling

- preemptive fixed priority. ex: task as software interrupt.

## scope

- bus a has no interrupt and no dma.
- 30hz sensor can talk only at 30hz.
- 8hz request and 8hz attitude control have different offsets.

## isr

| isr | released by | work |
| --- | --- | --- |
| `timer_8hz_0ms_isr` | timer | release `timer_8hz_0ms_task`. |
| `timer_128hz_isr` | timer | release `timer_128hz_task`. |
| `timer_30hz_isr` | timer | release `timer_30hz_task`. |
| `timer_8hz_50ms_isr` | timer | release `timer_8hz_50ms_task`. |
| `timer_1hz_isr` | timer | release `timer_1hz_task`. |
| `uart_tx_isr` | uart tx, `timer_1hz_task`, `flash_task` | send tx data. |

- 128hz, 8hz 0ms, and 8hz 50ms timers come from one clock.

## task table

| task | kind | device wait | deadline | priority | work |
| --- | --- | --- | --- | --- | --- |
| `timer_8hz_0ms_task` | periodic | no | 7.8ms | 1 | make 8hz request |
| `timer_128hz_task` | periodic | no | 7.8ms | 2 | send request to bus a, collect response |
| `timer_30hz_task` | periodic | no | 33ms | 3 | request 30hz sensor, read response next cycle |
| `timer_8hz_50ms_task` | periodic | no | 125ms | 4 | attitude control |
| `timer_1hz_task` | periodic | no | 1s | 5 | command and telemetry, mode, other control |
| `flash_task` | sporadic | yes | 60s | 6 | log store, dump read |

- `timer_8hz_0ms_task` deadline is 7.8ms, so request goes out before the next 128hz tick.
- `timer_8hz_0ms_task` is above `timer_128hz_task`, so request goes out at the same tick.

## matrix

| sender \ receiver | `uart_tx_isr` | `timer_8hz_0ms_task` | `timer_128hz_task` | `timer_30hz_task` | `timer_8hz_50ms_task` | `timer_1hz_task` | `flash_task` |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `timer_8hz_0ms_isr` | - | R | - | - | - | - | - |
| `timer_128hz_isr` | - | - | R | - | - | - | - |
| `timer_30hz_isr` | - | - | - | R | - | - | - |
| `timer_8hz_50ms_isr` | - | - | - | - | R | - | - |
| `timer_1hz_isr` | - | - | - | - | - | R | - |
| `timer_8hz_0ms_task` | - | - | D request | - | - | - | - |
| `timer_128hz_task` | - | - | - | - | D sensor a | - | - |
| `timer_30hz_task` | - | - | - | - | D sensor b | - | - |
| `timer_8hz_50ms_task` | - | - | - | - | - | D attitude | D attitude log |
| `timer_1hz_task` | R D telemetry | - | - | - | D mode and target | - | R D log and request |
| `flash_task` | R D dump | - | - | - | - | D store result | - |

- s band rx data is written by dma, so it has no sender row.

## device table

| device | accessor |
| --- | --- |
| timer 8hz 0ms channel | `timer_8hz_0ms_isr` |
| timer 128hz channel | `timer_128hz_isr` |
| timer 30hz channel | `timer_30hz_isr` |
| timer 8hz 50ms channel | `timer_8hz_50ms_isr` |
| timer 1hz channel | `timer_1hz_isr` |
| bus a | `timer_128hz_task` |
| 30hz sensor port | `timer_30hz_task` |
| actuator port | `timer_8hz_50ms_task` |
| s band rx | hw |
| uart tx | `uart_tx_isr` |
| other control device | `timer_1hz_task` |
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
