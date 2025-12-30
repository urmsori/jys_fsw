# cpu

- cpu provides cpu specific operations.
- cpu abstracts architecture differences.

## register access

- hardware register requires cache bypass.
- normal memory access may use cache.
- cache bypass guarantees direct hardware access.

## endian conversion

- cpu has native endian. big or little.
- hardware register may have fixed endian.
- protocol may require specific endian.
- conversion is needed between cpu and target endian.

## size

- 8bit, 16bit, 32bit, 64bit are supported.

## variant naming

- variant name follows official architecture name.
- variant name uses snake_case.