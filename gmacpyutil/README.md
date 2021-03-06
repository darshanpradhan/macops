# Overview
`gmacpyutil` is a set of python modules with useful methods for managing and controlling Macintosh computers.

# Features
All modules and methods should have helpful docstrings. Some of the highlights:
- `gmacpyutil` has several top-level methods
  - `RunProcess`, a full-featured wrapper for subprocess.Popen with support for sudo, background execution, streaming output, and more
  - `ConfigureLogging`, a convenience method to automatically configure syslog and, optionally, console logging
  - Reading and modifying plists with `GetPlist`, `GetPlistKey`, and `SetPlistKey`
- `gmacpyutil.airport` has methods to control WiFi interfaces
- `gmacpyutil.certs` has methods to manipulate certificates in the OS X Keychain
  - `FindCertificates` can find one or more certificates based on attributes such as issuer, subject, fingerprint, start and end dates, and more
  - `CreateIdentityPreference` will create a TLS Identity preference for a given cert
- `gmacpyutil.cocoadialog` has convenience methods to create and interact with CocoaDialog
- `gmacpyutil.ds` can read and modify Directory Service nodes
- `gmacpyutil.experiments` can list and determine experiment status
- `gmacpyutil.macdisk` has some wrappers around hdiutil, diskutil, and asr
- `gmacpyutil.profiles` can create and manipulate profiles
- `gmacpyutil.systemconfig` has methods to work with data in SCDynamicStore and SCPreferences
  - Read and change the computer name, hostname, and local name
  - Configure the system proxy

# Requirements
Most of the module will work with a default python installation.

`gmacpyutil.experiments` will need [PyYAML][] and `gmacpyutil.cocoadialog` will need [CocoaDialog][].

The tests have several requirements: [mox][], [mock][], and [google-apputils][]

# Customization
Most customization can be done by modifying `defaults.py`.

# Installation
```
$ python setup.py install
```

# Tests
Most of the code is covered with unit tests. These can be run by executing:
```
$ python setup.py test
```
or:
```
$ python -m unittest discover -p '*_test.py'
```

# Experiments
`gmacpyutil.experiments` can also be installed as a program.
```
$ python -m gmacpyutil.experiments --help
Usage: experiments.py [options]

Options:
  -h, --help            show this help message and exit
  -D, --debug
  -F, --formatted       Output experiments as one "experiment,status" per line
  -e MANUALLY_ENABLE, --enable=MANUALLY_ENABLE
                        Comma-delimited list of experiments to manually
                        enable.
  -d MANUALLY_DISABLE, --disable=MANUALLY_DISABLE
                        Comma-delimited list of experiments to manually
                        enable.
  -r RECOMMENDED, --recommended=RECOMMENDED
                        Comma-delimited list of experiments to no longer
                        manually manage.

```

The typical use is to deploy an `experiments.yaml` file (a sample is included in `data/experiments.yaml`) to every client. A new experiment is usually created with a `percentage` of `0`, and the experiment owner manually enables the experiment on her machine and tests. Once the tests pass, the owner will set `enable_unstable` to `true`, and verify there. Depending on the size of the fleet, she can also `enable_testing`. Then, she will gradually increase the `percentage` as she sees fit.

Each client machine uses a `MachineUUID` value stored in the file specified it `defaults.MACHINEINFO`. If that is missing, it uses the platform UUID from System Profiler (`system_profiler SPHardwareDataType`). It hashes the UUID with the name of the experiment and generates a number (`bucket`) from 0.0-100.0, and compares that to the `percentage`. If `bucket > percentage`, the experiment is enabled on the host.

Experiments at 100% are always enabled. Experiments at less than 100% can be manually enabled and disabled. If the machine's track is `unstable` or `testing` and `enable_unstable` and `enable_testing` attributes (respectively) are enabled, the experiment is enabled.

A set of `facter` facts can be generated by parsing the formatted output of the experiments script, using the `-F` argument. See `data/experiments.rb` for an example.

# Contact
We have a public mailing list at [google-macops@googlegroups.com](https://groups.google.com/forum/#!forum/google-macops).


  [PyYaml]: http://pyyaml.org/wiki/PyYAML
  [CocoaDialog]: http://mstratman.github.io/cocoadialog/
  [mox]: https://code.google.com/p/pymox/
  [mock]: http://www.voidspace.org.uk/python/mock/
  [google-apputils]: https://code.google.com/p/google-apputils-python/
