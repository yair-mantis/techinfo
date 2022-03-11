the original version in html in the link below
https://sigull.github.io/techinfo/

# Introduction of how to build OpenVDB Python 3.7 module (pyopenvdb) in Windows 10 x64 environment

use a virtual enviroment inside python3.7+ (tested on 3.10)
```
python -m venv venv
./venv/scripts/activate
pip3 install numpy cmake
```

## 2. Install vcpkg tag: 2020.04 to C:\dev

```powershell
cd c:\dev  
git clone https://github.com/microsoft/vcpkg.git -b 2020.04  
cd vcpkg  
bootstrap-vcpkg.bat  
./vcpkg.exe install zlib:x64-windows  
./vcpkg.exe install blosc:x64-windows  
./vcpkg.exe install openexr:x64-windows  
./vcpkg.exe install tbb:x64-windows  
./vcpkg.exe install boost-iostreams:x64-windows  
./vcpkg.exe install boost-system:x64-windows  
./vcpkg.exe install boost-any:x64-windows  
./vcpkg.exe install boost-algorithm:x64-windows  
./vcpkg.exe install boost-uuid:x64-windows  
./vcpkg.exe install boost-interprocess:x64-windows  
./vcpkg.exe install pybind11:x64-windows  
#use above python virtual env
./vcpkg.exe install boost-python:x64-windows
```

## 3. Download and Install Cmake
https://cmake.org/download/  
if you didnt use the one above via pip
    
## 4. Building OpenVDB
### 4.1 Download OpenVDB from GitHub
`git clone https://github.com/AcademySoftwareFoundation/openvdb.git`
    
### 4.2 Change NUMPY option to ON

```
cd openvdb\openvdb\openvdb\python  
```
edit `CMakeList.txt`  

Change the `USE_NUMPY` and `OPENVDB_PYTHON_WRAP_ALL_GRID_TYPES` option in CMakeList.txt to `ON`.  
```cmake
option(USE_NUMPY [=[  
Build the python library with numpy support. Currently requires CMake 3.14.]=] ON)  
option(OPENVDB_PYTHON_WRAP_ALL_GRID_TYPES [=[  
Expose (almost) all of the grid types in the python module. Otherwise, only FloatGrid, BoolGrid and  
Vec3SGrid will be exposed (see, e.g., exportIntGrid() in python/pyIntGrid.cc). Compiling the Python  
module with this ON can be very memory-intensive.]=] ON)
``` 


    
### 4.3 Build Python module of OpenVDB
```powershell
mkdir build  
cd build  
cmake -DCMAKE_TOOLCHAIN_FILE=c:\dev\vcpkg\scripts\buildsystems\vcpkg.cmake -DVCPKG_TARGET_TRIPLET=x64-windows -A x64 -DOPENVDB_BUILD_PYTHON_MODULE=ON ..  
```

observe

``` cmake
        -- ----------------------------------------------------  
        -- ------------ Configuring OpenVDBPython -------------  
        -- ----------------------------------------------------  
        -- Found Python: C:/dev/vcpkg/installed/x64-windows/include/python3.7) (found suitable version `3.7.3`, minimum required is `2.7`)  
        -- Found NumPy: C:/Python37/Lib/site-packages/numpy/core/include (found suitable version `1.19.2`, minimum required is `1.12.1`)  
        -- Found boost_python37  
        -- Found boost_numpy37
```
    
You will find `boost_python37` and `boost_numpy37` 

```powershell
  cmake --build . --parallel 4 --config Release  
  cd openvdb\openvdb\python\Release  
  dir  
  boost_numpy37-vc142-mt-x64-1_72.dll  
  boost_python37-vc142-mt-x64-1_72.dll  
  Half-2_3.dll  
  pyopenvdb.exp  
  pyopenvdb.lib  
  pyopenvdb.pyd  
  python37.dll  
  tbb.dll  
```    

You will need other dependency DLL(`bloc.dll`, `lz4.dll`, `openvdb.dll`, `zlib.dll`, `zstd.dll`, these DLL find in the vcpkg and openvdb folder.  

**
