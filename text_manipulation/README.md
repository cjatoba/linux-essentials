## Replace text

```
sed -i 's/ORIGINAL_TEXT/NEW_TEXT/' .file_name
```

## Print first part of text ($2 print secound etc...)
```
awk '{print $1}'
```

## Print variable in sed command
```
sed -i "s/$var1/ZZ/" "$file"
```

## Replace space with underscore
```
sed -e 's/ /_/g'
```

## Convert Lowercase Characters
```
rename -v 'y/a-z/A-Z/' *.txt
```

## Convert Uppercase Characters
```
rename -v 'y/A-Z/a-z/' *.TXT
```
