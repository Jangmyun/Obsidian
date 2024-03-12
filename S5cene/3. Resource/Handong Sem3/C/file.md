# file handling

```c
File *fptr;
fptr = fopen(filename, mode);
```

#### mode
##### `w`- Write  /   `a`- Appends new data to a file  /  `r` - read from a file


### Create a File
```c
File *fptr;
// create a file
fptr = fopen("filename.txt", "w");
```

### Append Content To a File
```c
FILE *fptr;  

fptr = fopen("filename.txt", "a");
fprintf(fptr, "\nHi everybody!");  
fclose(fptr);
```

### Read a File
```c
File *fptr;

fptr = fopen("fileName.txt", "r");

char myStr[100];
fgets(myStr, 100,fptr);
fclose(fptr);
```