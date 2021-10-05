# robotics-controller-framework

This is a generic framework for communicating between a microcontroller (like an Arduino, STM32, Teensy, etc.) and a higher-power "hub" controller (e.g. Raspberry Pi, Jetson, or PC).

## purpose
In a multi-processor robotics (or really almost any multi-controller embedded) application, it can be painful to re-engineer a full-duplex messaging system for every project. This framework doesn't attempt to condense or optimize message size, though down the road that could be a goal, if you're looking for something data-dense or supports compression, try protobufs or something. This is supposed to be easy to use, easy to understand (especially for beginners in robotics/software), and simple to implement.

## protocol

There is a minimal message set used to communicate between the controllers:
* Handshake
    * >"I'm awake and listening!"
    * Before the initialization message is sent, the two processors first "shake hands" to establish that both processors are listening. To `handshake` each processor begins sending the asterisk (\*) character to the other processor until they each receive it and are ready to send the initialization message. When the handshake is complete, both processors flush their respective "receive" buffers before attempting to recieve another message. This prevents a backup of handshaking messages if one processor boots significantly faster than the other.
* Heartbeat
    * >"I'm alive!"
    * Bi-directional: Device reports a heartbeat to the master and the master reports a heartbeat to the device.
    * Lost heartbeat detection: Both the device and the master can detect a missing heartbeat from the other processor.
    * Health reporting: Heartbeats report actual time between heartbeats for diagnosing when a processor is overloaded.
    * **Fixed message length**
    * |"H"|\<counter>|\<milliseconds since last heartbeat sent>|
* Initialization
    * >"Here's a little about me."
    * This is a JSON message used to let the other processor know all of the interfaces available.
    * **Variable message length**
    * |"J"|\<designator>|\<payload>|
* Request
    * >"I need to know the value of \<some field>."
    * Allows one processor to request information from the other processor using designators from the initialization JSON files.
    * A request expects a `response` from the other processor.
    * **Fixed message length**
    * |"R"|\<designator>|\<payload>|
* Response
    >* "Here's the information you requested."
    * Replies to a `request` from the other processor.
    * **Variable message length**
    * |"r"|\<designator>|\<payload>|
* Control
    * >"I need you to do \<something> now."
    * Allows one processor to set a control variable on the other processor.
    * Expects an "ACK" (`0x07`) back
    * **Variable message length**
    * |"C"|\<designator>|\<payload>|
* IRQ
    * >"I really want you to know about this, but do with it what you will."
    * Allows a processor to push an "important" message to the other processor unsolicited (i.e. without requiring a `reqest` to first send the data).
    * **Variable message length**
    * |"!"|\<designator>|\<payload>|
