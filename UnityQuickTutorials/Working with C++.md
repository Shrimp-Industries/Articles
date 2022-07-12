Created: 15.06.2020 \
Tags: #gamedev #shrimpindustries #youtube \
YT: https://www.youtube.com/watch?v=qljLhXfVt78 \
GH: https://github.com/Shrimpey/UnityUnmanagedPlugins \
# Working with C++
## Introduction
How to create a backend code in C++ that can be run in Unity? Do you need math heavy operations to be done in C++ for better performance? Or do you just need the access to some raw C++ functions in a library?
Watch the detailed video linked at the top of the page or read about the basics here.
## Main idea
You can create backend code in C++ and use it in Unity provided that your code is compiled to a shared library (DLL[^1] in Windows or SO[^2] in Linux) and follows some rules and restrictions. In Unity it can also be referred to as a Native or Unmanaged Plugin[^3].

---

To do so, create a C++ project with a header and implementation file. Those two files will define our communication interface. In the header file wrap it up with standard include guards[^4] and include a line:
```cpp
#define DLL_EXPORT __declspec(dllexport)
```
The line above is only necessary for creating Windows specific DLL. In case you're compiling for Linux, this is not needed. This magic line will make sure our functions are exported with proper names to DLL file [^5].

Next thing you need to do is to place all the functions you want to export in an `extern "C"` block like so:

```cpp
extern "C"{
	int DLL_EXPORT SimpleTypeReturnFun();
}
```

Similarily create an implementation file that will define function's behaviour (in this case it will return some integer).

---

Then compile the project into DLL/SO, for example like this if you're using MINGW-64[^6] in terminal:
```bash
>g++ -m64 -fpic -shared backend.cc -o backend.dll
```
`m64` flag makes sure to use 64-bit compiler (as you're probably working on a 64-bit machine), `fpic`[^7] flag stands for position independent code and is necessary for a shared library to be properly read by Unity later on , `-shared` creates a shared library.

---

Place created DLL/SO file anywhere in Unity Assets folder (this needs to be done while Unity is closed). Create a C# script in Unity and include following code:

```csharp
[DllImport("backend")]
private static extern int SimpleTypeReturnFun();

private void Start(){
	Debug.Log(SimpleTypeReturnFun());
}
```

This should properly trigger the backend function and print an integer that was returned in the implementation file of our c++ project. The `DllImport`[^8] attribute[^9] must contain the name of our shared library (extension is not needed).

---

More detailed examples can be viewed at: https://github.com/Shrimpey/UnityUnmanagedPlugins

## References
[^1]: https://en.wikipedia.org/wiki/Dynamic-link_library
[^2]: https://iq.opengenus.org/create-shared-library-in-cpp/
[^3]: https://docs.unity3d.com/Manual/NativePlugins.html
[^4]: https://en.wikipedia.org/wiki/Include_guard
[^5]: https://docs.microsoft.com/en-us/cpp/build/exporting-from-a-dll-using-declspec-dllexport?view=msvc-170
[^6]: https://www.mingw-w64.org/
[^7]: https://renenyffenegger.ch/notes/development/languages/C-C-plus-plus/GCC/options/f/pic/index
[^8]: https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.dllimportattribute?view=net-6.0
[^9]: https://docs.unity3d.com/Manual/Attributes.html
