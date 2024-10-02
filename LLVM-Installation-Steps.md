

1. Download all the pre-requisites:
   sudo apt -y install gcc g++ git cmake ninja-build zlib1g-dev

2. Cloning the repository and building from source:
   i. Configure Git: Check for git is configured or not:
      git config user.email
      git config user.name
      If not configured, then use the following:
      git config --global user.email "saha.puja21.ps@gmail.com"
      git config --global user.name "puja2196"

    ii. Clone the repository: git clone https://github.com/llvm/llvm-project.git
        Without history: git clone https://github.com/llvm/llvm-project.git –-depth=1
        This Git command clones the latest source code from GitHub into a local directory named llvm-project.
        
        If you want to work with a specific release of a specific version:
        If you want to check out the first release of LLVM 17 into a branch called llvm-17, you type the following:
        git checkout -b llvm-17 llvmorg-17.0.1

3. Create the build directory:
   cd llvm-project/
   mkdir build
   
4. Generate the build system files:
   cmake -G Ninja -DCMAKE_BUILD_TYPE=Release -DLLVM_ENABLE_PROJECTS="clang;lld" -B ~/llvm-tutorial/llvm-project/build -S ~/llvm-tutorial/llvm-project/llvm

   -G : For which build system we are generating build system files
   -B : Where the path of the build directory
   -S : To specify the source directory

   The generation process can be influenced by setting various variables with the –D option.
   Usually, they are prefixed with CMAKE_ (if defined by CMake) or LLVM_ (if defined by LLVM).
   -DCMAKE_BUILD_TYPE(=Release/=Debug)
   -DLLVM_ENABLE_PROJECTS="clang;lld" => what are the projects you want to build along side llvm

   For out-of-source tree development of passes and using the LLVM Libraries for the same, while building llvm,
   we need to add a flag "-DLLVM_LINK_LLVM_DYLIB=ON" for creating shared objects and installed in /usr/local/lib
   which can be loaded dynamically while loading the pass.

5. Finally, compile and install LLVM.
   cmake --build build / cd build && ninja -j32
   This will compile llvm, clang and lld.

   After building all, to check all are working as expected or not, use:
   cmake --build build --target check-all

   check-llvm instead of check-all runs the LLVM tests but not the clang and lld tests; 
   check-llvm-codegen runs only the tests in the CodeGen directory from LLVM (that is, the llvm/test/CodeGen directory).

6. As the last step, you can install the binaries:
   sudo cmake --install build / sudo ninja install

If some thing is failing in any step: start from step 4.

7. Managing the source code:
   1. Sync with the llvm-project directory:



Building Out-of-Source Tree LLVM passes and loading dynamically:

1. ~/llvm-tutorial/llvm-trial 
2. Write the PPProfiler.cpp and CMakeLists.txt as given in the folder.

Building the plugin:

3. rm -rf build/
4. mkdir build && cd build
5. cmake .. && make


6. Verifying the Output of opt:

You have hello.ll in ~/llvm-tutorial/llvm-trial folder and now test with opt:
opt -load-pass-plugin ./build/libPPProfilerPass.so -passes=ppprofiler -S hello.ll -o output.ll

You can pass more than one passes to opt:
opt -load-pass-plugin ./build/libPPProfilerPass.so -passes="ppprofiler,default<O2>" -S hello.ll -o output.ll

7. You can link your pass to clang in the following way:
   1. Add registerPipelineStartEpCallBack() to registerCB() function in .cpp file.
   2. Compile the .c file with clang and -fpass-plugin option => generates a.out.
      ==> clang -fpass-plugin=./build/libPPProfilerPass.so hello.c
   3. Run the a.out: ./a.out

Implementing a new pass as a plugin is useful if you plan to use it with a precompiled clang, for example.
On the other hand, if you write your own compiler, then there can be good reasons to add your new passes directly to the LLVM source tree. 
There are two different ways you can do this – as a plugin and as a fully integrated pass.
