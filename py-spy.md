# Profiling Salt

cProfile and other common profiler tools generally assume you are profiling a function that returns, or a single process, which is not the case in environments like Salt. Often a developer will want to study a component which includes Tornado, maybe even multiprocessing, and it is difficult to use those tools to get a good picture.

[py-spy](https://github.com/benfred/py-spy) is a relatively newer Python profiler and fits perfectly with the Salt use case being lower-level than the Python interpreter (actually it's written in Rust) all of the above problems do not really exist. Moreover the tool is very low-overhead and outputs some nice [flame graphs](http://www.brendangregg.com/flamegraphs.html).

Example:
![](https://raw.githubusercontent.com/benfred/py-spy/master/images/flamegraph.svg?sanitize=true)

It even attaches to existing Python processes without source changes! All you have to do is:

```bash
py-spy --nonblocking --pid $PID --flame output.svg --duration 10
```

Installation can be done via pip or cargo, a quick and dirty way in SUSE distros is:

curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
pip install py-spy

