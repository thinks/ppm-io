# PNM IO
This repository implements reading and writing of images in the [PNM formats](https://en.wikipedia.org/wiki/Netpbm_format). More specifically, reading and writing of RGB (PPM) and greyscale (PGM) images is supported. The PPM and PGM image formats are extremely simple, making them ideal for small exploratory projects. The major benefit of this simplicity is that is it possible to implement the these file formats without using external dependencies such as compression libraries. All production code in this repository is implemented in a single [header file](https://github.com/thinks/ppm-io/blob/master/include/thinks/pnm_io/pnm_io.h), making it very simple to add to an existing project without having to set up additional linker rules. Also, the implementation uses only standard types and holds no state, meaning it should be fairly straight-forward to use the read and write functions. Detailed documentation is available in the source code.

All code in this repository is released under the [MIT license](https://en.wikipedia.org/wiki/MIT_License).

## Clone
If you just want to use the single [header file](https://github.com/thinks/ppm-io/blob/master/include/thinks/pnm_io/pnm_io.h) to read/write images in your project you can simply clone this repository using the snippet below.
```bash
$ git clone https://github.com/thinks/pnm-io.git
```
In order to run the tests you need to clone recursively since the [Catch2](https://github.com/catchorg/Catch2) test framework is included as a submodule. To clone and run the tests use the following snippet (similar for `Debug`).
```bash
$ git clone --recursive https://github.com/thinks/pnm-io.git
$ mkdir build && cd build
$ cmake ../pnm-io
$ cmake --build . --config Release
$ ctest . -C Release --verbose
```

## Usage
The implementation supports both reading and writing of RGB (PPM) and greyscale (PGM) images. We provide some brief usage examples here, additional examples can be found in the [examples](https://github.com/thinks/ppm-io/blob/master/examples/) and [test](https://github.com/thinks/ppm-io/blob/master/test/) folders.

The examples below demonstrate reading images. The functions taking an `std::istream` are the most flexible, since they do not assume that the image is stored on disk. These versions are useful for testing since they allow the tests to run in memory, avoiding file permission related issues. However, since images stored on disk are probably the most likely real-life scenario convenience versions that take a file name are also provided.
```cpp
#include "thinks/pnm_io/pnm_io.h"

// RGB (PPM)
auto width = std::size_t{0};
auto height = std::size_t{0};
auto pixel_data = std::vector<std::uint8_t>{};
auto ifs = std::ifstream("my_file.ppm", ios::binary);
thinks::ReadPpmImage(ifs, &width, &height, &pixel_data);
ifs.close();

// ... or more conveniently.
thinks::ReadPpmImage("my_file.ppm", &width, &height, &pixel_data);

// Greyscale (PGM)
auto width = std::size_t{0};
auto height = std::size_t{0};
auto pixel_data = std::vector<std::uint8_t>{};

auto ifs = std::ifstream("my_file.pgm", ios::binary);
thinks::ReadPgmImage(ifs, &width, &height, &pixel_data);
ifs.close();

// ... or more conveniently.
thinks::ReadPgmImage("my_file.pgm", &width, &height, &pixel_data);
```
Writing image files is done in a similar fashion. Again, convenience functions taking file names are provided. 
```cpp
#include "thinks/pnm_io/pnm_io.h"

// Write a 10x10 RGB (PPM) image where all pixels have the value (128, 128, 128).
constexpr auto width = std::size_t{10};
constexpr auto height = std::size_t{10};
auto pixel_data = std::vector<std::uint8_t>(width * height * 3, 128);
auto ofs = std::ofstream("my_file.ppm", ios::binary);
thinks::WritePpmImage(ofs, width, height, pixel_data.data());
ofs.close();

// ... or more conveniently.
thinks::WritePpmImage("my_file.ppm", width, height, pixel_data.data());

// Write a 10x10 greyscale (PGM) image where all pixels have the value 128.
constexpr auto width = std::size_t{10};
constexpr auto height = std::size_t{10};
auto pixel_data = std::vector<std::uint8_t>(width * height, 128);
auto ofs = std::ofstream("my_file.pgm", ios::binary);
thinks::WritePgmImage(ofs, width, height, pixel_data.data());
ofs.close();

// ... or more conveniently.
thinks::WritePgmImage("my_file.pgm", width, height, pixel_data.data());
```
