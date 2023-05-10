# orbcode-scripts
### How-to-trace using SWO and ORBTrace mini
Compile and upload (any) sketch for Arduino Portenta H7 / M7 core:
```bash
git clone https://github.com/arduino/arduino-examples && cd arduino-examples
arduino-cli compile -b arduino:mbed_portenta:envie_m7 -v examples/01.Basics/Blink -u -p /dev/ttyACM1
```
Note down the path to where the ELF-file is stored and edit [.gdbinit](.gdbinit) accordingly.

Start GDB using .gdbinit contained in this repository:
```bash
ln -s .gdbinit-swo .gdbinit
arm-none-eabi-gdb -iex 'add-auto-load-safe-path .'
```
Start [orbuculum](https://github.com/orbcode/orbuculum):
```bash
orbuculum --verbose 3 --monitor 1000
```
Create a folder for captured SWO trace data and start [orbfifo](https://github.com/orbcode/orbuculum):
```bash
cd /tmp
mkdir swo
orbfifo --verbose 3 --basedir swo/
```
Now you are ready to observe some trace data, i.e.
```bash
cat swo/hwevent
2,1,**SLEEP**
2,1,**SLEEP**
2,1,0x08053e48
2,1,**SLEEP**
2,1,**SLEEP**
```
or even better:
```bash
orbtop -e /tmp/arduino/sketches/817DC06D31EFAD33501697C59B5B9880/Blink.ino.elf -l
```
which results in i.e.
```bash
 96.50%    34272 ** Sleeping **
  1.05%      374 HAL_PCD_IRQHandler
  0.23%       83 hal_critical_section_enter
  0.23%       83 core_util_critical_section_enter
  0.19%       70 lp_ticker_read
  0.18%       66 update_present_time
  0.15%       56 hal_critical_section_exit
  0.13%       49 void mbed::internal::do_sleep_operation<mbed::internal::timed_predicate_op
  0.12%       46 core_util_critical_section_exit
  0.10%       36 mbed::internal::timed_predicate_op::wake_condition() const
-----------------
 98.88%    35135 of  35514  Samples

[---H] Interval = 999mS
```

### How-to-trace using TRACECLK / TRACED0 and ORBTrace mini
```bash
ln -s .gdbinit-trace .gdbinit
arm-none-eabi-gdb -iex 'add-auto-load-safe-path .'
...
c
Ctrl+C
enableTraceSTM32H747_M7
c
....
```
