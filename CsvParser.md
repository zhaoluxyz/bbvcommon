# Introduction #

The CsvParser is an easy to use component that provides functionality to parse csv formatted strings.

The only method provided is:

```
public string[] Parse(string line, char separator)
```

You pass in the string you want to parse and the character that is used as separator.

The result is an array of strings containing the values – or columns – that were separated by the specified character.


# Samples #

The samples are taken from the unit tests:

## Comma ##

```
"\"5\",\"ABC\",Test \"XYZ,,,,,\"9, Strasse\",\"\",,\"8200\",\"Ort\",\"CH\""
```

Result:
```
"5", 
"ABC", 
"Test \"XYZ"
"", 
"", 
"", 
"", 
"9, Strasse", 
"", 
"", 
"8200", 
"Ort", 
"CH"
```

## Double quotes ##

```
"\"5\"\"ABC\",\"Test X\",\"\"\"\"\"\"\"\",\"9, S\",\"\"\"\",\"8\"\"Ort\"\"CH\""
```

Result:
```
"5\"ABC",
"Test XYZ",
"\"\"\"", 
"9, Strasse",
"\"", 
"8200\"Ort\"CH"
```

## Spaces and empty strings ##

```
"\"00501\", \"ABC\" ,\"Test XYZ\",";
```

Result:
```
"00501",
" \"ABC\" ",
"Test XYZ",
""
```

## Without quotes ##

```
"personal_id;nachname;vorname;";
```

Result:
```
"personal_id",
"nachname",
"vorname"
```