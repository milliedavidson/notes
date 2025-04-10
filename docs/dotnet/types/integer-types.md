# Integer types

- ```sbyte``` = signed 8-bit integer
- ```byte``` = unsigned 8-bit integer
- ```short``` = signed 16-bit integer
- ```ushort``` = unsigned 16-bit integer
- ```int``` = signed 32-bit integer
- ```uint``` = unsigned 32-bit integer
- ```long``` = signed 64-bit integer
- ```ulong``` = unsigned 64-bit integer

## C# specific integer types

- ```nint``` = native signed integer
- ```nuint``` = native unsigned integer

The size of these types depends on the **runtime platform**; they will be 32 bits on 32 bit systems and 64 bits on 64 bit systems. They differ to a regular ```int``` because ```int``` maps to the ```System.Int32``` value type (which is always a 32 bit value, regardless of the runtime platform).  

## The difference between signed and unsigned

- **Signed integers** can represent both **postive** and **negative** numbers
- **Unsigned integers** can only represent **positive** numbers

## Assigning values

If you assign a value that is out of range, you will get a compiler error. For example, this value is invalid because a ```byte``` can only store values from 0 to 255:

```byte age = 256;```
