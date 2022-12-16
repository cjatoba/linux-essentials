## Replace text

```bash
sed -i 's/ORIGINAL_TEXT/NEW_TEXT/' .file_name
```

## Print first part of text ($2 print secound etc...)
```bash
awk '{print $1}'
```

## Print variable in sed command
```bash
sed -i "s/$var1/ZZ/" "$file"
```

## Replace space with underscore
```bash
sed -e 's/ /_/g'
```

## Returns the first occurrence of a pattern
```bash
sed -n '/2022-11-10/{p;q;}' file.txt
```

## Returns the last occurrence of a pattern
```bash
sed -n '/2022-11-10/h;${x;p;}' file.tct
```

## Convert Lowercase Characters
```bash
rename -v 'y/a-z/A-Z/' *.txt
```

## Convert Uppercase Characters
```bash
rename -v 'y/A-Z/a-z/' *.TXT
```
