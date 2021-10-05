# JSON Config Documentation

## Type
  * Name
    * `Required`
    * (The name is mostly for developer use, but it is recommended to stick to these for the sake of everyone's sanity)
      * bool
      * char
      * int16
      * int32
      * string-short
      * string-long
      * string-very-long
      * uint8
      * uint16
      * uint32
      * vec2
      * vec3
      * vec4
  * Values
    * `Optional`
    * How many values are associated with this type?
    * If not specified, the default is 1.
    * Useful for creating vector types like coordinates or matrices
    * If `values` is `-n` where n>0, then the first `n` bytes after the designator tell how long the message is in bytes (Useful for variable length strings).
  * Bits
    * `Optional`
    * Bit depth- how many bits deep is the data in this control? This assumes the bits are right-aligned.
  * Bytes
    * `Required`
    * How many bytes per value in this type? (for a 3-d vector where each coordinate is a byte, `values` will be 3 and `bytes` will be 1)
  * Scale
    * `Optional`
    * Multiplicative scaling to apply to this type
    * (Useful for transmitting non-integers since only integer types are supported)
    * Applies to all elements when `Values` is greater than one.
    * Default is 1
  * Description
    * `Optional`
    * Mostly a developer tool to keep track of what each type is representing.

