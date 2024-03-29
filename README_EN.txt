* README_EN.txt
* 2023.03.18
* sgp4

1. DESCRIPTION
2. SOURCES
3. PATCHES
4. DEPENDENCIES
5. EXTERNALS
6. AUTHOR

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
`sgp4` library fork

-------------------------------------------------------------------------------
2. SOURCES
-------------------------------------------------------------------------------
https://github.com/dnwrnr/sgp4
https://www.danrw.com/sgp4/
https://travis-ci.org/dnwrnr/sgp4

-------------------------------------------------------------------------------
3. PATCHES
-------------------------------------------------------------------------------
The original library patched to fix these (all) issues:

1. Fix trigonometric range before call and after call to triginometric
   functions because of sloppy QD arithmetic outside and inside a function
   call.
2. All double's replaced by dd_real from QD library to decrease precision
   fluctuation to the minimum in certain cases.
3. uint64_t in DateTime and all time related classes replaced by floating point
   value to avoid truncation of the ticks fraction part.

The original library patched to fix only these critical (p1) issues:

1. Fix trigonometric range before call and after call to triginometric
   functions.

All patches improved precision from ~1 meter per 100km altitude along
velocity vector in certain routines up to 10^-7 meters per 100km altitude along
velocity vector.

-------------------------------------------------------------------------------
4. DEPENDENCIES
-------------------------------------------------------------------------------
`CMakeLists.txt`:

* `tacklelib`

Third-party libraries:

* `qd`

-------------------------------------------------------------------------------
5. EXTERNALS
-------------------------------------------------------------------------------
To checkout externals you must use the
[vcstool](https://github.com/dirk-thomas/vcstool) python module.

NOTE:
  To install the module from the git repository:

  >
  python -m pip install git+https://github.com/dirk-thomas/vcstool

-------------------------------------------------------------------------------
6. AUTHOR
-------------------------------------------------------------------------------
Andrey Dibrov (andry at inbox dot ru)
