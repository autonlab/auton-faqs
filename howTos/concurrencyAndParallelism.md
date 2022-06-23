## Profiling
Before we make our code more performant (with or without parallelization) it's important to profile so we know where the greatest benefit of optimization lies.

For python a good default is [cProfile](https://docs.python.org/3/library/profile.html#module-cProfile). Checkout the docs for a good reference, but for a simple way to profile a function called `myAwesomeFunction` checkout the below:
```
import cProfile
from pstats import SortKey

cProfile.run("myAwesomeFunction()", 'profiledOutput.prof', sort=SortKey.CUMULATIVE)
```

### Visualizing profiled results
With the resultant 'profiledOutput.prof' you see above there's a package called [snakeviz](http://jiffyclub.github.io/snakeviz/) with which we can visualize our results!
```
snakeviz profiledOutput.prof
```

The above command will kick off a local browser where you can drilldown into specific bottlenecks and their constituent function calls.


## Concurrency / Parallelization
*Parallelism* is when multiple cpus are engaged in tasks at the same time - of which multiprocessing on a multi-core machine in python is a good example. 

*Concurrency* is when multiple tasks overlap but may be context-switched by a single cpu to give the illusion of parallelism. Multithreading in python is a good example of this, given python's Global Interpreter Lock (GIL), which prevents multiple processes from interpreting python at the same time.


**Note** Parallelism is possible in python because processes don't share memory space, as threads do - so each process is actually running its own instance of python interpreter


To apply each of these you have several options in python, with some packages offering both:

**Multiprocessing python packages**: [joblib](https://joblib.readthedocs.io/en/latest/generated/joblib.Parallel.html), [multiprocessing](https://docs.python.org/3/library/multiprocessing.html)

**Multithreading python packages**: [asyncio](https://docs.python.org/3/library/asyncio.html), [threading](https://docs.python.org/3/library/threading.html), [joblib](https://joblib.readthedocs.io/en/latest/generated/joblib.Parallel.html)
### When to use what
- `asyncio` and `threading` are both pure multithreading libraries
- `multiprocessing` offers multiprocessing, and seems to strike a nice balance between control and ease-of-use
- `joblib` makes for extreme ease-of-use, at the expense of defining who-knows-what defaults (well somebody knows, but I sure don't). It also offers more than parallelization, and is an overall, well, job library - offering cached results and job pipelines as well as multithreading and multiprocessing.

### Using joblib
An example use of joblib's parallelization is below. Note all it requires is an iterable input and it'll apply and equally distribute the work accordingly.
```
  pds = Parallel(n_jobs=7)(delayed(featurizeSubDF)(int(fin), group, shortSeries.columns, searchDirectory, dataconfig.features_nk) 
      for fin, group in shortSeries.groupby('fin_study_id'))
```

## Advanced optimizations
- For limiting multiprocessed jobs to stay on their designated core of execution (rather than the libs you use commandeering cores) define the environment variables as described in this [stackoverflow](https://stackoverflow.com/questions/30791550/limit-number-of-threads-in-numpy)
- Got any more tips? Add them here :)