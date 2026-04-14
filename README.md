# hack-for-sabnzbd-on-weird-platforms

... with weird platforms: Platforms like RISC-V where not all python3 packages are there pre-built

Only needed if run SABnzbd from source.

## All cases

Prepare
```
sudo apt install python3-dev # for building sabctools
sudo apt install libffi-dev # for building cffi
```

## rustc available

If rustc and cargo are available for your paltform, install them and build normally.

For example, on Ubuntu (even on RISC-V):

```
sudo apt install rustc cargo -y
```
Installation will take 400 MB disk space.

After that, install the python packages (if needed, some will be compiled with the c compiler, other with rustc).

If that fails, for example because of too old rustc / cargo, go to the next paragraph.


## No rustc available

Copy requirements.txt to requirements-no-rust.txt, and in requirements-no-rust.txt

- set cryptography to 3.3.2
- comment out orjson

```
$ diff requirements.txt requirements-no-rust.txt
38c38
< cryptography>=3.0
---
> cryptography==3.3.2
43c43
< orjson==3.11.8; python_version > '3.9'
---
> #orjson==3.11.8; python_version > '3.9'
```


## Brute force


Comment out some things:


```
sed -i '/cryptography/s/^\([[:space:]]*\)/\1# /' SABnzbd.py 
sed -i.bak -E '/^(cryptography|ujson|orjson)/s/^/# /' requirements.txt
```

## Installing packages and running SABnzbd

Install required python3 packages, and run SABnzbd:
```
venv/bin/python3 -m pip install -r <name-of-correct-requirements-file> -U
venv/bin/python3 SABnzbd.py
```

## Errors you might see without the above changes



```
Collecting cryptography>=3.0 (from -r requirements.txt (line 38))
  Downloading cryptography-46.0.7.tar.gz (750 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 750.7/750.7 kB 1.1 MB/s eta 0:00:00
  Installing build dependencies ... error
  error: subprocess-exited-with-error

  × pip subprocess to install build dependencies did not run successfully.
  │ exit code: 1
  ╰─> [33 lines of output]
      Ignoring cffi: markers 'platform_python_implementation != "PyPy" and python_version == "3.8"' don't match your environment
      Collecting maturin<2,>=1.9.4
        Downloading maturin-1.13.1.tar.gz (340 kB)
           ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 340.4/340.4 kB 237.0 kB/s eta 0:00:00
        Installing build dependencies: started
        Installing build dependencies: finished with status 'done'
        Getting requirements to build wheel: started
        Getting requirements to build wheel: finished with status 'done'
        Installing backend dependencies: started
        Installing backend dependencies: finished with status 'done'
        Preparing metadata (pyproject.toml): started
        Preparing metadata (pyproject.toml): finished with status 'error'
        error: subprocess-exited-with-error

        × Preparing metadata (pyproject.toml) did not run successfully.
        │ exit code: 1
        ╰─> [6 lines of output]
            /tmp/pip-build-env-hq2tok1i/overlay/lib/python3.12/site-packages/setuptools/_vendor/wheel/bdist_wheel.py:4: FutureWarning: The 'wheel' package is no longer the canonical location of the 'bdist_wheel' command, and will be removed in a future release. Please update to setuptools v70.1 or later which contains an integrated version of this command.
              warn(
            Python reports SOABI: cpython-312-riscv64-linux-gnu
            Computed rustc target triple: riscv64-unknown-linux-gnu
            Target triple not supported by rustup: riscv64-unknown-linux-gnu
            Rust not found, installing into a temporary directory
            [end of output]

        note: This error originates from a subprocess, and is likely not a problem with pip.
      error: metadata-generation-failed

      × Encountered error while generating package metadata.
      ╰─> See above for output.

      note: This is an issue with the package mentioned above, not pip.
      hint: See above for details.
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
error: subprocess-exited-with-error

× pip subprocess to install build dependencies did not run successfully.
│ exit code: 1
╰─> See above for output.

note: This error originates from a subprocess, and is likely not a problem with pip.
```
or

```
            error: failed to parse manifest at `/tmp/pip-install-wtpqe33v/maturin_79d3c58523db4cd7b8e823c543e6b602/Cargo.toml`

            Caused by:
              feature `edition2024` is required

              The package requires the Cargo feature called `edition2024`, but that feature is not stabilized in this version of Cargo (1.75.0).
              Consider adding `cargo-features = ["edition2024"]` to the top of Cargo.toml (above the [package] table) to tell Cargo you are opting in to use this unstable feature.
              See https://doc.rust-lang.org/nightly/cargo/reference/unstable.html#edition-2024 for more information about the status of this feature.
            error: `cargo metadata --manifest-path Cargo.toml --format-version 1 --locked` failed with code 101
```

