Create a in-source pass and build and load it as a dynamic plugin:

1. Create a folder "PPProfiler" in llvm-project/llvm/lib/Transforms directory.
2. Create the cpp file in the directory.
3. In the same folder, create CMakeLists.txt with the following content to register the pass in llvm source tree:
   add_llvm_pass_plugin(PPProfiler PPProfiler.cpp)
4. Update CMakeLists.txt in the parent directory "llvm-project/llvm/lib/Transforms" with the following content:
   add_subdirectory(PPProfiler)

5. Build llvm tools:
    cmake -G "Ninja" -DCMAKE_BUILD_TYPE=Release -DLLVM_ENABLE_PROJECTS="clang;lld" -S ~/llvm-tutorial/llvm-project/llvm -B ~/llvm-tutorial/llvm-project/build
    This will give output as: -- Registering PPProfiler as a pass plugin (static build: OFF)

    sudo ninja install
    This will create .so file and install it in /usr/local/lib/ directory.

Create a in-source pass and build and link this to the llvm source tree it as a static pass:
1. cmake -G "Ninja" -DLLVM_PPPROFILER_LINK_INTO_TOOLS=ON -DCMAKE_BUILD_TYPE=Release -DLLVM_ENABLE_PROJECTS="clang;lld" -S ~/llvm-tutorial/llvm-project/llvm -B ~/llvm-tutorial/llvm-project/build
   ==> This will give output as: -- Registering PPProfiler as a pass plugin (static build: ON)

2. sudo ninja install
   ==> This will create .a file and install it in /usr/local/lib/ directory.

And you can see this in the file: /usr/local/include/llvm/Support/Extension.def 
//extension handlers
HANDLE_EXTENSION(PPProfiler)
#undef HANDLE_EXTENSION
