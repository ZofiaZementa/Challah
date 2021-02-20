# Challah

The friendly chat application.

# Building

## Windows

You first need to download Qt for Windows, from [the Qt Website](https://www.qt.io/download), you'll need qt-5 (~ 5.12.2) and the MinGW compiler.
Also, QtCreator is pretty handy to have, since it sets up all the qbs profiles etc. for you.
You'll also need MinGW msys to compile protobuf.

Clone the [protobuf repository](https://github.com/protocolbuffers/protobuf) and switch the branch to the one which is currently referenced in `vendor/protobuf` (currently `3.12.x`).

Go into Msys and install (using `pacman -Sy`) `msys/autoconf` and `mingw64/mingw-w64-x86_64-gcc` (there may be other dependencies needed, you'll figure it out, maybe look [here](https://github.com/protocolbuffers/protobuf/tree/master/src)).
You'll then probably need to add `/mingw64/bin` to the path (`export PATH=/mingw64/bin:$PATH`) for it to find the executables you just installed.
Step into the protobuf directory, and execute `./autogen.sh` and `./configure --prefix=<outdir-for-binaries>`. Make sure that you can access the `<outdir-for-binaries>` from windows.
Then run `make -j<number-of-cpu-threads>` and `make install` (if you run `make check` inbetween, it'll probably spit out errors, but the other two shouldn't).
You have now built protobuf.

For convenience, just download the protobuf compiler for the version you need from [the protobuf github page](https://github.com/protocolbuffers/protobuf/releases), the archive you need is called `protobuf-<version>-win64.zip`.
Unpack that to a convenient location.

Now open the `Challah.qbs` file in QtCreator.
Configure it to use MinGW, then open the Projects tab, and under MinGW -> Release -> build -> Properties input `project.vendoredKirigami:true project.vendoredProtobuf:true`.
Then open the `Source.qbs` in the Challah folder in the project tree, and somewhere at the top, input

```
protobuf.cpp.includePath: "<outdir-for-protobuf-binaries>/include"
protobuf.cpp.libraryPath: "<outdir-for-protobuf-binaries>/lib"
protobuf.cpp.compilerPath: "<unpacked-protobuf-compiler-dir>/bin/protoc.exe"
```

Open the `protobuf.qbs` in the protobuf folder in the project tree.
At the bottom where it says `cmake`, input `C:/Qt/Tools/CMake_64/bin/cmake.exe` (or a path to another `cmake` binary).
In line ~17, replace `product.sourceDirectory+"/protobuf/cmake/libprotobuf.a"` with `"<outdir-for-protobuf-binaries>/lib/libprotobuf.a"` (be sure to make a backup-copy of `libprotobuf.a`, on a failed build it likes to delete it).
Now you should (probably) be ready to go and can hit build.

The build will probably fail because it can't find `lundefined`, but thats expected (this probably is to some value not being set in the qbs).
Copy the failed command from the log, copy it into Msys, remove the `-lundefined`, replace the backslashes in the very first part of the command with forwardslashes and hit it.
You now built Challah!

Now onto running it.

You'll need to copy more or less the whole content of `C:\Qt\5.15.2\mingw81_64\bin` into the directory of `Challah.exe` (probably something like `<path-to-parentdir-of-Challah>\build-Challah-Desktop_Qt_5_15_2_MinGW_64_bit-Release\Release_Desktop__b164590d0f1a9a49\Challah.81be376b`).
Also copy the whole folders `C:\Qt\5.15.2\mingw81_64\plugins\platforms` and `C:\Qt\5.15.2\mingw81_64\qml` into that folder.
Now you (only) need to get OpenSSl or something similar, for examply from [here](https://slproweb.com/products/Win32OpenSSL.html) (you could of course also read it).
