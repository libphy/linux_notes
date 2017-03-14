# Window-Unix Commands
### Windows cmd vs linux/unix bash    

In the commands below, **bold** texts such as **myfolder** can be any name you want. *Italic texts* are for explaining.   

| Function       | cmd command           | bash command  |
| ------------- |:-------------| :-----|
|Print a name for current working directory|cd| pwd|
|Make a new folder under current directory    | mkdir **myfolder** | mkdir **myfolder** |
| Show things in the current directory      | dir     |   ls  |
| Show hidden directory/files | dir /a </br> dir /a:d  (*for directories*)</br> dir /a:h  (*for files*)     |    la -a |
|Navigate into a subdirectory| cd **myfolder** | cd **myfolder** |
|Navigate back to a directory one level up| cd ..| cd ..|
|Navigate back to your home directory| *see the tips below* | cd ~/ |
|Create a new empty file in current directory|copy nul > **myfile.txt**|touch **myfile.txt**|
|Copy an existing file to another|copy **myfile.txt** > **myfile2.txt** </br> copy **myfile.txt** > **myfolder/myfile.txt** |cp **myfile.txt**  **myfile2.txt** </br> cp **myfile.txt**  **myfolder/myfile.txt** |
|Move files to a different folder| move **myfile2.txt** **myfolder/myfile2.txt** |mv **myfile2.txt** **myfolder/myfile2.txt** |
|Rename a file| move **myfile.txt** **myfile1.txt**| mv **myfile.txt** **myfile1.txt**|
|Remove a file| del **myfile1.txt**|rm **myfile1.txt**|
|Remove an empty directory| rmdir **Foo**|rm **Foo**|
|Remove a directory and its contents recursively|rmdir /s **myfolder**|rm -r **myfolder** </br> rm -rf **myfolder** (*To force remove all contents*)|


Tips:  
- If your file or folder name has a space, you can wrap with a quotation mark- e.g.`cd "my folder/myfile.txt"`
- In Windows cmd, to see what is your home path: `echo %HOMEPATH%`.     
If you have a HOMEPATH, you can go there by typing `cd %HOMEPATH%`.    
If you don't have one or want to change to a different location- check it out [here](http://stackoverflow.com/questions/11928078/permanently-change-environment-variables-in-windows) or just google how to set HOMEPATH in cmd.

### Useful keyboard shortcuts inside the cmd or bash
| Function       | cmd shortcut           | bash shortcut  |
| ------------- |:-------------| :-----|
|Copy from the shell|`ctrl`+c| `shift`+`ctrl`+c (*linux*) </br> `Command`+c (*mac*)|
|Paste to the shell|`ctrl`+v| `shift`+`ctrl`+v (*linux*) </br> `Command`+v (*mac*)|
