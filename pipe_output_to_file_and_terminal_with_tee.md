# Pipe output to file and active terminal

For situations where you normally want output to be saved in a file but you also want to see the output as it occurs in the terminal. I use this for most scripts since it ends up being appropritae for production use (log files) and saves time when debugging (log files and shows the output immediately).

```
#!/bin/bash
# tee_example.sh
PREFIX=example
OUT=$PREFIX_stdout.txt
ERR=$PREFIX_stderr.txt
echo "Some process with output" > >(tee $OUT) 2> >(tee $ERR >&2)
```

Run this and you'll immediately see output.

```
$ ./tee_example.sh
Some process with output
```

You'll also find the same output in the log files. It doesn't matter how you pipe stuff. In this example OUT goes to one file and ERR goes to another.

```
$ cat example_stdout.txt
Some process with output
```
