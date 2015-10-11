# Overview #
The security package provides classes for generating and handling hash values from different input data sources.
Features:
  * Generating hash values from files or strings with MD5/SHA1 algorithm.
  * Comparing generated hash values from files or strings.
  * Easy change of hash algorithm.
  * Easy change of data sources.


# Usage #
A simple way to generate hash values is to create a `HashService` object with an instance of the `IHashAlgorithm`, for example `MD5Algorithm`:

```
IHashService service = new HashService(new MD5Algorithm());
```

To generate the hash value from a string use:

```
service.GetHashFromString(“text”);
```

To generate the hash value from a specified input use:

```
ITextReader reader = new TextFileReader(“c:/test.txt”);
service.GetHashFromReader(reader);
```

To write a generated hash value from a string to the specified output use:

```
ITextWriter writer = new TextFileWriter(“c:/test.md5”);
service.WriteHashFromString(“text”, writer);
```

To write a generated hash value from a file to the specified output use:

```
service.WriteHash(reader, writer);
```

To compare a generated hash value from a string to an existing hash value use:

```
service.CompareHash(“text”, “1CB251EC0D568dE6A929B520C4AED8D1”);
```

To compare a generated hash value from a string to an existing hash value in a file use:

```
ITextReader hashReader = new TextFileReader(“c:/hash.md5”);
service.CompareHash(“text”, hashReader);
```

To compare a generated hash value from a specified input to an existing hash value in a file use:

```
service.CompareHash(reader, hashReader);
```

To compare a generated hash value from a specified input to an existing hash value use:

```
service.CompareHash(reader, “1CB251EC0D568dE6A929B520C4AED8D1”);
```

# Define Custom Hash Algorithm #
To define a custom HashAlgorithm, you have to create a class which implements the `IHashAlgorithm` interface.
Custom hash algorithm class:

```
public class HashAlgorithm : IHashAlgorithm
{
    public HashAlgorithm()
    {}

    public string ComputeHash(string text)
    {}

    public string ComputeHashFromStream(Stream stream)
    {}
}
```

# Define custom `TextReader` and `TextWriter` #
To define custom `TextReader` and `TextWriter` for the input or output data for the `HashService`, you have to create classes which implements the `ITextReader` or `ITextWriter` interface.

## Custom `TextReader` class ##

```
public class TextURLReader : ITextReader
{
    private readonly string url;

    public TextStreamReader(string url)
    {}

    public string GetString()
    {}

    public Stream GetStream()
    {}
}
```

## Custom `TextWriter` class ##

```
public class TextDataBaseWriter : ITextWriter
{
    private readonly string connectionString;
    private readonly string sql;

    public TextStreamWriter(string connectionString, string sql)
    {}

    public void Write(string text)
    {}
}
```