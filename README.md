# Cdo.{rb,py} - Use Ruby/Python to access the power of CDO

Welcome to the scripting interfaces of [CDO](https://code.zmaw.de/projects/cdo/wiki)!
This repository contains interfaces for [Ruby](http://www.ruby-lang.org) and [Python](https://www.python.org). If you are not sure, wether this is useful or not, please have a look at:
[Why the .... should I use this???](https://code.zmaw.de/projects/cdo/wiki/Cdo%7Brbpy%7D#Why-the-)

## Installation

Releases are distributed via [pypi](https://pypi.org/project/cdo) and [rubygems](https://rubygems.org/gems/cdo):

*  Ruby
```
    gem install cdo
```
*  Python
```
    pip install cdo
```

### Requirements

Cdo.{rb,py} requires a working CDO binary and Ruby 2.x or Python 2.7/3.x
The Ruby module interface (available as 'cdo_lib') works with ruby-1.9.x, too.
**Please note, that it will be removed in the future.** -- It's not thread-safe and can be 
fully replaced by the class interface.

Multi-dimensional arrays (numpy for python, narray for ruby) require addtional
netcdf-io modules. These are [scipy](https://docs.scipy.org/doc/scipy/reference/io.html) or [python-netcdf4](https://pypi.python.org/pypi/netCDF4) for python and
[ruby-netcdf](https://rubygems.org/gems/ruby-netcdf) for ruby. Because scipy has some difficulties with netcdf, I strongly recommend python-netcdf4. 

## Usage

You can find a lot of examples in the unit tests for both languages. Here are the direct links to the [ruby tests](https://github.com/Try2Code/cdo-bindings/blob/master/ruby/test/test_cdo.rb) and the [python tests](https://github.com/Try2Code/cdo-bindings/blob/master/python/test/test_cdo.py).

The following describes the basic features for both languages

### Run operators

The Ruby module can be used directly after loading it. For python and ruby
class interfaces an instance has to be created first

```ruby
    cdo = Cdo.new   #ruby
```
```python
    cdo = Cdo()     #python
```

Please check the documentation for constructor paramaters. I try to have equal interfaces in both languages for all public methods.

#### Debugging

For debugging purpose, both interfaces provide a "debug" attribute. If it is set to a boolian true, the complete commands and the return values will be printed during execution

```ruby
    cdo.debug = true    #ruby
```
```python
    cdo.debug = True    #python
```

The default is false of cause.

#### File information
```ruby
        cdo.infov(input: ifile)        #ruby
        cdo.showlevels(input: ifile)

```
```python
        cdo.infov(input=ifile)         #python
        cdo.showlevels(input=ifile)
```

#### Operators with user defined regular output files
```ruby
        cdo.timmin(input: ifile ,output: ofile)       #ruby
```
```python
        cdo.timmin(input = ifile,output = ofile)      #python
```
By default the return value of each call is the name of the output files (no matter if its a temporary file or not)

#### Use temporary output files
```ruby
        tminFile = cdo.timmin(input: ifile)  #ruby
```
```python
        tminFile = cdo.timmin(input = ifile) #python
```

#### Operators with parameter
```ruby
        cdo.remap([gridfile,weightfile],input:   ifile, output: ofile)   #ruby
```
```python
        cdo.remap([gridfile,weightfile],input => ifile, output => ofile) #python
```

#### logging
```ruby
        cdo = Cdo.new(logging: true, logFile: 'cdo_commands.log') #ruby
```
```python
        cdo = Cdo(logging=True, logFile='cdo_commands.log')       #python
```

#### Set global CDO options
```ruby
        cdo.copy(input:  ifile, output:  ofile,options:  "-f nc4")     #ruby
```
```python
        cdo.copy(input = ifile, output = ofile,options = "-f nc4")     #python
```

#### Set environment variables
```ruby
        cdo.splitname(input:    ifile.join(' '), output:    'splitTag',env: {'CDO_FILE_SUFFIX' => '.nc'}) #or
        cdo.env = {'CDO_FILE_SUFFIX' => '.nc'}
```
```python
        cdo.splitname(input = ' '.join(ifiles) ,  output =  'splitTag', env={"CDO_FILE_SUFFIX": ".nc"})   #or
        cdo.env = {'CDO_FILE_SUFFIX': '.nc'}
```

#### Return multi-dimension arrrays
```ruby
        temperatures = cdo.fldmin(:input => ifile,:returnArray => true).var('T').get   (rb, version < 1.2.0)
        temperatures = cdo.fldmin(:input => ifile,:returnCdf => true).var('T').get    (rb, version >= 1.2.0)
        temperatures = cdo.fldmin(:input => ifile,:returnArray => 'T')                (rb, version >= 1.2.0)
```
```python
        temperatures = cdo.fldmin(input = ifile,returnArray = True).variables['T'][:] (py, version < 1.2.0)
        temperatures = cdo.fldmin(input = ifile,returnCdf = True).variables['T'][:]   (py, version >= 1.2.0)
        temperatures = cdo.fldmin(input = ifile,returnArray = 'T')                   (py, version >= 1.2.0)
```

*) If you use scipy >= 0.14 as netcdf backend, you have to use following code
instead to avoid possible segmentation faults:
```python
    cdf = cdo.fldmin(input = ifile,returnCdf = True)
    temperatures = cdf.variables['T'][:]
```
More examples can be found in test/cdo-examples.rb and [on the homepage](https://code.zmaw.de/projects/cdo/wiki/Cdo%7Brbpy%7D)

### Avoid re-processing

If you do not want to re-compute files, you can set

*  the instance attribute 'forceOutput' to false: this will effect all later call of that instance **or**
*  the operator option 'forceOutput' to false: this will only effect this operator call of this instance

For more information, please have a look at the unit tests.

## Support, Issues, Bugs, ...

Please use the forum or ticket system of CDOs official web page:
http://code.zmaw.de/projects/cdo

## Changelog
* next:
  - return arrays/lists of output files, which are created by split* operators suggestion from Karl-Hermann Wieners :ocean:
* **1.3.2** [2016-10-24]
  - improvened stdout/stderr handling, thx to jvegasbsc
* **1.3.1**
  - fix environment handling per call (ruby version)
* **1.3.0**
  - require ruby-2.*
  - support for upcomming CDO release 1.7.1
  - improve loggin for ruby
  - introduce logging for python
  - unicode bugfix - thanks to Sebastian Illing (illing2005) [python-only]
* **1.2.7**
  - Added class interface for ruby version 2.x, mainly for thread safety
* **1.2.6**
  - bugfix for autocompletion in interactive usage [python-only]
* **1.2.5**
  - bugfix for environment handling (Thanks philipp) [python-only]
  - add logging [ruby-only]
* **1.2.4**
  - support python3: Thanks to @jhamman
  - bugfix for scipy: Thanks to @martinclaus
  - docu fixes: Thanks to @guziy
  - allow environment setting via call and object construction (see test_env in test_cdo.py)
* **1.2.3**
  - bugfix release: adjust library/feature check to latest cdo-1.6.2  release
* **1.2.2**
  - allow arrays in additions to strings for input argument
  - add methods for checking the IO libraries of CDO and their versions
  - optionally return None on error (suggestion from Alex Loew, python only)
* **1.2.1**
  - new return option: Masked Arrays
    if the new keyword returnMaArray is given, its value is taken as variable
    name and a masked array wrt to its FillValues is returned
    contribution for python by Alex Loew
  - error handling: return stderr in case of non-zero return value + raise exception
    contribution for python from Estanislao Gonzalez
  - autocompletion and built-in documentation through help() for interactive use
    contribution from Estanislao Gonzalez [python]
  - Added help operator for displaying help interactively [ruby]
* **1.2.0** API change:
  - Ruby now uses the same keys like the python interface, i.e. :input and :output
    instead of :in and :out
  - :returnArray will accept a variable name, for which the multidimesional
    array is returned
* **1.1.0** API change:
  - new option :returnCdf : will return the netcdf file handle, which was formerly
    done via :returnArray
  - new options :force : if set to true the cdo call will be run even if the given
    output file is presen, default: false

---

## [Thanks to all contributors!](https://github.com/Try2Code/cdo-bindings/graphs/contributors)

## License

Cdo.{rb,py} makes use of the GPLv2 License, see COPYING

---

# Other stuff
```
Author   | Ralf Mueller <stark.dreamdetective@gmail.com>
Requires | CDO version 1.5.x or newer
License  | Copyright 2011-2017 by Ralf Mueller Released under GPLv2 license
         | See the COPYING file included in the distribution
```
