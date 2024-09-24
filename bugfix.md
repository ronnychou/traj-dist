# Bug fix record

``` shell
git clone https://github.com/bguillouet/traj-dist.git
```

to local to install manually.

``` shell
python setup.py install
# or pip install .
```

Maybe we miss `Cython` package.

``` shell
conda install Cython
# or pip install Cython
```

## CompileError caused by type conversion error

``` python
Error compiling Cython file:
------------------------------------------------------------
...
    q=len(Q)
 
    cc=_compute_critical_values(P,Q,p,q)
    eps=cc[0]
    while(len(cc)!=1):
        m_i=len(cc)/2-1
                     ^
------------------------------------------------------------
 
traj_dist/cydist/frechet.pyx:535:21: Cannot assign type 'double' to 'int'
......
  File ".../site-packages/Cython/Build/Dependencies.py", line 1134, in cythonize
    cythonize_one(*args)
  File ".../site-packages/Cython/Build/Dependencies.py", line 1301, in cythonize_one
    raise CompileError(None, pyx_file)
Cython.Compiler.Errors.CompileError: traj_dist/cydist/frechet.pyx
```

change to `m_i=len(cc)//2-1`

## error: Microsoft Visual C++ 14.0 or greater is required. during pip install

It indicate that this library requires to install Microsoft Visual C++ 14.0 or greater. To install that, we need to go https://visualstudio.microsoft.com/visual-cpp-build-tools/ and download the program, then install Workload named `Desktop development C++` (may plus `.NET desktop development`), install the core and their optional files.

## ModuleNotFoundError

### caused by no compilation

``` python
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "traj_dist/distance.py", line 13, in <module>
    from .cydist.sspd import c_e_sspd, c_g_sspd
ModuleNotFoundError: No module named 'traj_dist.cydist.sspd'
```

`sspd` need be compiled from `.pyx` into `.so` or `.pyd`.

Use command below to compile them.

``` python
python setup.py build_ext --inplace
```

`--inplace`: ignore build-lib and put compiled extensions into the source directory alongside your pure Python modules.

### caused by absolute import in .pyx files

``` python
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "traj-dist/traj_dist/distance.py", line 3, in <module>
    from .cydist.sspd import c_e_sspd, c_g_sspd
  File "traj_dist/cydist/sspd.pyx", line 8, in init traj_dist.cydist.sspd
    from basic_euclidean import c_point_to_trajectory
ModuleNotFoundError: No module named 'basic_euclidean'
```

check every file with suffix of `.pyx` in cydist, and add `.` ahead of `basic_euclidean` or `basic_geographical` to make the relative import instead of absolute import.

Then re-compile them

``` python
python setup.py build_ext --inplace
```

After fixing bugs, `python setup.py install` again.
