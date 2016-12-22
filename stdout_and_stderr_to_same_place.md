# Pipe STDOUT and STDERR to the same place

A quick one that is commonly used. Pipe all output, error or otherwise, to the same place. Append `2>&1`.

```
# Error and normal output to the same file
./my_script.sh > /home/jfalkner/output.txt 2>&1
```

A related case is if a cron task is spamming output to `/var/spool/mail/jfalkner`. Append this to the task.

```
# crontab -e
*/15 * * * * /home/jfalkner/some_task.sh > /dev/null 2>&1
```
