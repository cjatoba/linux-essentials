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
sed -i "s/$var1/ZZ/g" "$file"
```
