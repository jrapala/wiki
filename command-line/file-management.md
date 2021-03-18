# File Management



## Commands

**Copy File to New File:** `$ cp beep.txt message.txt` - Copy the contents of `beep.txt` to a new file called `message.txt`

**Copy File to Directory:** `$ cp beep.txt ../` - Copy the contents of `beep.txt` to a new file called `beep.txt` in the parent directory.

**Copy Files to Directory:** `$ cp beep.txt book.txt cool` - Copy contents of two files to new files in `cool`.

**Copy All Files In a Directory, Including Subdirectories**: `$ cp -r xyz newzyx` (recursively copies) (`-R` and `--recurvise` do the same thing)

**Rename or Overwrite Files and Directories** `$ mv message.txt msg.txt` renames `message.txt` to `msg.txt` (or overwrites `msg.tx` if that existed). You don't have to worry about moving files inside a directory during this.

**Make a New Directory** `$ mkdir`. Add `-p` command to make nested directories (e.g. `$ mkdir -p a/b/c/d`). If directory `a` already exists, then the new subdirectories are added to it.

**Tab** Pressing tab autocompletes. Pressing tab twice will list all the items that may apply.

**Wild Card (*)** `$ cat *.txt` will list the contents of all the text files in the directory. `$ cat b*p.txt`

**Delete Files** `$ rm book.txt`

**Delete Empty Directories:** `$ rmdir hello`

**Delete Directory with Files:** `$ rm -r hello`

**Count Number of Lines/Words/Bytes** `$ wc notes.txt` Add `-l`, `-w` or `-c` (characters/bytes) to specify.

**End-of-file Signal**: If you type a command without an argument, write your file, and then press Ctrl+D



## Brace Expansion

Brace expansion generates patterns that allow for shortcuts. List items inside `{}` separated by commas.

`$ mv moby-dick.txt{_,}` expands to `$ mv moby-dick.txt_ moby-dick.txt`. Common element is `moby-dick.txt` and the different elements are inside the braces.

`$ cat b{ee,oo}p.txt`

`$ echo {a,b}{c,d}{x,y,z}` = `acx acy acz adx ady adz bcx bcy bcz bdx bdy bdz`

`$ mkdir img{0..100}` makes directories named `img0` to `img100`. 

`$ mkdir img{0..100..10}` adds a step, so img0, 1mg10, etc..

**Note:** You can always test your command with `echo` in front of it.



## Paths

Paths that start with `.` or `..` are relative paths.

Paths that start with `/` are absolute paths. `pwd` gives you the current absolute path.



## Output Redirect (Writing to a File)

Commands, by default, print to stdout, so you can read it in the terminal.

Use `>` to write the output of a command to a file instead. It will overwrite contents if the file already exists.

​	`$ echo ahoy thar > greetings.txt` saves "ahoy thar" to greetings.txt

The `>>` operator appends to the end of a file if it already exists.

​	`$ echo wow >> greetings.txt` adds another line that says "wow" (TTY causes the new line)



## Input Redirect (Reading from a File)

You can read a file into stdin with `<`.

​	`$ wc -c < notes.txt` (make the assumption that `wc` can't get a filename argument)

