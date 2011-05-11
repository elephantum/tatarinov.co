---
title: hello world
---

This is a simple "hello world" post. Just to test things.

{% highlight python %}
import os
import itertools

CHUNK_SIZE = 100

def read_lines_backwards(filename):
    f = file(filename, 'rb')
    f.seek(0, os.SEEK_END)

    left = ''

    while f.tell() > 0:
        bytes_to_read = min(f.tell(), CHUNK_SIZE)

        # read chunk and return to original position
        f.seek(-bytes_to_read, os.SEEK_CUR)
        buf = f.read(bytes_to_read) + left
        f.seek(-bytes_to_read, os.SEEK_CUR)
        
        lines = buf.split('\n')
        
        for i in reversed(lines[1:]):
            yield i

        left = lines[0]

    yield left

def tail(filename, n):
    it = read_lines_backwards(filename)
    line = it.next()
    
    # skip all empty lines at the end of file
    while line == '':
        line = it.next()

    res = [line] + list(itertools.islice(it, n-1))

    return list(reversed(res))
{% endhighlight %}