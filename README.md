# runcache
:wrench: shell command caching

## Examples

Caching `date`

```console
$ runcache date
Mon Jan 18 11:23:25 CET 2016
$ runcache date
Mon Jan 18 11:23:25 CET 2016
$ date
Mon Jan 18 11:23:35 CET 2016
```

Caching `sleep`

```console
$ time runcache sleep 5
runcache sleep 5  0.01s user 0.02s system 0% cpu 5.031 total
$ time runcache sleep 5
runcache sleep 5  0.01s user 0.02s system 99% cpu 0.028 total
$ time runcache sleep 5
runcache sleep 5  0.01s user 0.02s system 113% cpu 0.024 total
```

Caching a complex command using `sh -c ...`

```console
$ time runcache sh -c 'echo running a long command...; sleep 5; echo finished'
running a long command...
finished
runcache sh -c 'echo running a long command...; sleep 5; echo finished'  0.01s user 0.02s system 0% cpu 5.043 total
$ time runcache sh -c 'echo running a long command...; sleep 5; echo finished'
running a long command...
finished
runcache sh -c 'echo running a long command...; sleep 5; echo finished'  0.01s user 0.02s system 106% cpu 0.026 total
```

Caching the execution of a Python file + changing its content to invalidate the cache

```console
$ cat test.py
from time import sleep
print "hello",
sleep(5)
print "world 5"

$ time runcache python test.py
hello world 5
runcache python test.py  0.03s user 0.04s system 1% cpu 5.095 total

$ time runcache python test.py
hello world 5
runcache python test.py  0.01s user 0.02s system 110% cpu 0.032 total

$ sed -i '' s/5/3/ test.py

$ cat test.py
from time import sleep
print "hello",
sleep(3)
print "world 3"

$ time runcache python test.py
hello world 3
runcache python test.py  0.03s user 0.03s system 1% cpu 3.063 total

$ time runcache python test.py
hello world 3
runcache python test.py  0.01s user 0.02s system 100% cpu 0.036 total

$ sed -i '' s/3/5/ test.py

$ cat test.py
from time import sleep
print "hello",
sleep(5)
print "world 5"

$ time runcache python test.py
hello world 5
runcache python test.py  0.01s user 0.02s system 108% cpu 0.030 total
```
