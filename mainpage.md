# Introduction #

Arduino library for communicating with KlikAanKlikUit (KaKu) devices using a 433Mhz tranceiver or with separate receiver and transmitter modules

The library works asynchronously using the Timer1 interrupt running at a 175us period.

There are separate receive and transmit buffers.

both the old type modules as the new type modules (automatic code) are supported

For these new type modules, the group and set dim level commands are also supported

Frames being sent are autmaically repeated five times (configurable)
Received frame are filtered for duplicates. (unless they arrive more than ~10 seconds apart)

# Details #

Main library functions:
  * `kk_init()` initialize the library, starts the timer interrupt
  * `kk_available()` Check if there is anything in the receive buffer
  * `kk_receive(&address,&unit,&cmd,&dimlevel)` Get a frame from the receive buffer.
  * `kk_send(address,unit,cmd,dimlevel)` Put a frame in the send buffer