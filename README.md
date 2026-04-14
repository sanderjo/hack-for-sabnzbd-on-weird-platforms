# hack-for-sabnzbd-on-weird-platforms

... with weird platforms: Platforms like RISC-V where not all python3 packages are there pre-built, and building them from source would require Rust (`Rust not found, installing into a temporary directory`).

Only needed if run SABnzbd from source.

Prepare
```
sudo apt install python3-dev # for building sabctools
sudo apt install libffi-dev # for building cffi
```

Comment out some things:


```
sed -i '/cryptography/s/^\([[:space:]]*\)/\1# /' SABnzbd.py 
sed -i.bak -E '/^(cryptography|ujson|orjson)/s/^/# /' requirements.txt
```

Install required python3 packages, and run SABnzbd:
```
venv/bin/python3 -m pip install -r requirements.txt -U
venv/bin/python3 SABnzbd.p
```

```
~/git/sabnzbd$ venv/bin/python3 -m pip install -r requirements.txt -U
Ignoring pywin32: markers 'sys_platform == "win32"' don't match your environment
Ignoring windows-toasts: markers 'sys_platform == "win32"' don't match your environment
Ignoring winrt-runtime: markers 'sys_platform == "win32"' don't match your environment
Ignoring winrt-Windows.Data.Xml.Dom: markers 'sys_platform == "win32"' don't match your environment
Ignoring winrt-Windows.Foundation: markers 'sys_platform == "win32"' don't match your environment
Ignoring winrt-Windows.Foundation.Collections: markers 'sys_platform == "win32"' don't match your environment
```

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

