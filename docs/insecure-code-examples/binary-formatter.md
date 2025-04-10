# Binary formatter

## Insecure example ⚠️

```csharp
using System;
using System.IO;
using System.Runtime.Serialization.Formatters.Binary;

public class InsecureBinaryFormatterExample
{
    // Insecure method to deserialize an object from a file
    public static object DeserializeFromFile(string filePath)
    {
        using (FileStream fs = new FileStream(filePath, FileMode.Open))
        {
            BinaryFormatter formatter = new BinaryFormatter();
            
            // INSECURE! BinaryFormatter.Deserialize can execute arbitrary code
            return formatter.Deserialize(fs);
        }
    }

    public static void Main()
    {
        try
        {
            // This could be loading data from an untrusted source
            object obj = DeserializeFromFile("user-provided-data.bin");
            Console.WriteLine($"Loaded object: {obj}");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }
}
```

## Why Binary Formatter is dangerous

The `BinaryFormatter` class is fundamentally insecure because:

1. **Remote Code Execution (RCE)**: It deserializes entire object graphs and can execute code during the deserialization process. An attacker can craft malicious serialized data that executes arbitrary code when deserialized.

2. **Denial of Service (DoS)**: Attackers can create "deserialization bombs" - small payloads that expand to consume enormous amounts of memory or CPU when deserialized (similar to XML bombing).

3. **Type Confusion Attacks**: The formatter can be tricked into creating objects of unexpected types, potentially bypassing security checks.

4. **Officially Deprecated**: Microsoft has officially deprecated `BinaryFormatter` due to these security issues and recommends against its use in any scenario.

## Secure example 1

```csharp
// Option 1: Use System.Text.Json (modern, secure)
using System.Text.Json;

public static T DeserializeJsonSecurely<T>(string json)
{
    var options = new JsonSerializerOptions
    {
        // Prevent deserialization to unexpected types
        TypeInfoResolver = System.Text.Json.Serialization.Metadata.JsonTypeInfoResolver.Specifiable
    };
    
    return JsonSerializer.Deserialize<T>(json, options);
}
```

## Secure example 2

```csharp
// Option 2: Use Newtonsoft.Json with security settings
using Newtonsoft.Json;

public static T DeserializeNewtonsoftJsonSecurely<T>(string json)
{
    var settings = new JsonSerializerSettings
    {
        // Prevent type loading attacks
        TypeNameHandling = TypeNameHandling.None
    };
    
    return JsonConvert.DeserializeObject<T>(json, settings);
}
```

When handling serialization, always:
1. Use modern libraries like `System.Text.Json` or `Newtonsoft.Json` with secure configuration
2. Avoid deserialising data from untrusted sources
3. If type information must be included, implement a strict allow-list of permitted types
4. Consider using data contracts to explicitly define what can be serialized/deserialised
