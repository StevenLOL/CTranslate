# CTranslate

CTranslate is a C++ implementation of OpenNMT's `translate.lua` script, with no Torch/Lua dependencies. It facilitates the use of OpenNMT models in existing products and on various platforms using [Eigen](http://eigen.tuxfamily.org) as a backend.

It supports CPU OpenNMT models. You can convert GPU trained models with the [`release_model.lua`](https://github.com/OpenNMT/OpenNMT/tree/master/tools#release-model) script.

## Dependencies

* `C++11`
* `CMake`
* `Boost`
* `Eigen` > 3.3

## Compiling

```
$ mkdir build
$ cd build
$ cmake -DEIGEN_ROOT=<path to Eigen library> ..


#if eigen is not found then you will have this error:
# /data/gits/OpenNMT/CTranslate/include/onmt/Eigen/MatrixBatch.h:3:23: fatal error: Eigen/Dense: No such file or directory
compilation terminated.

#Solution:
$ sudo apt install libeigen3-dev
$ cmake -DEIGEN_ROOT=/usr/ ..

```

It will produce the dynamic library `libonmt.so` and the translation client `cli/translate`.

To compile only the library, use the `-DLIB_ONLY=ON` flag.

Unless you are cross-compiling for a different architecture, you can add `-DCMAKE_CXX_FLAGS="-march=native"` to the cmake command above to optimize for speed.

## Using

### Client

See `cli/translate --help`. It has the same interface as the `translate.lua` script in OpenNMT while adding the ability to work with the standard input and output.

### Library

This project is also a convenient way to load OpenNMT models and translate texts in existing software.

Here is a very simple example:

```
//        src/demo.cc
#include <iostream>

#include <onmt/TranslatorFactory.h>

int main()
{
  // Create a new Translator object.
  auto translator = onmt::TranslatorFactory::build("enfr_model_release.t7");

  // Translate a tokenized sentence.
  std::cout << translator->translate("Hello world !") << std::endl;

  return 0;
}

Insert following to ProjectRoot/MamkeLists.txt

add_executable(demo src/demo.cc)
target_link_libraries(demo ${PROJECT_NAME})

Then in build folder build it again.


```

For a more advanced usage, see:

* `include/onmt/TranslatorFactory.h` to instantiate a new translator
* `include/onmt/ITranslator.h` to translate sequences or batch of sequences
* `include/onmt/TranslationResult.h` to retrieve results and attention vectors

## Missing features

* OpenNMT tokenization and detokenization
