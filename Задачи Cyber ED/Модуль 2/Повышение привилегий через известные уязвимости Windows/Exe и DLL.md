Задача 1: создаем EXE вызывающий MessageBox
```

#include <iostream>  
#include <windows.h>  
using namespace std;

int main() {  
    MessageBox(NULL, TEXT("Hello, World!"), TEXT("My Message Box"), MB_OK);  
    return 0;  
}
```


![Pasted image 20241115131619](https://github.com/user-attachments/assets/bf135b15-1888-40b1-b820-3a2ee404311d)

Задача 2: создаем DLL для EXE вызывающий MessageBox
```

2.1 Преобразуем основной исполняемый файл (MessageBoxExe.exe)

#include <iostream>

#include <windows.h>

using namespace std;

int main() {

    HINSTANCE hMessageLib = ::LoadLibraryW(L"MessageLib.dll");

    if (!hMessageLib) {

        std::cout << "Cannot Load DLL" << std::endl;

        return -1;

    }

    MessageBox(NULL, TEXT("Hello, World!"), TEXT("My Message Box"), MB_OK);

    return 0;

}

2.2 Создадим dll (MessageLib.dll)

#include "MessageLib.h"

BOOL APIENTRY DllMain(HMODULE hModule,

       DWORD ul_reason_for_call,

       LPVOID lpReserved

)

{

       switch (ul_reason_for_call)

       {

       case DLL_PROCESS_ATTACH:

                    ::MessageBoxW(NULL, L"Hello from Dllmain MessageLib", L"MessageLib", 0);
					print_hello(); // вывод print_hello
             case DLL_THREAD_ATTACH:

                    case DLL_THREAD_DETACH:

                          case DLL_PROCESS_DETACH:

                          break;

       }

       return TRUE;

}

int print_hello() {

       ::MessageBoxW(NULL, L"Hello from print_hello", L"MessageLib", 0);

       return 0;

}

2.3 Создадим заголовок (messagelib.h)

#pragma once

#include <Windows.h>

#include <stdio.h>

#include <string>

extern "C" __declspec(dllexport) int __stdcall print_hello();
```

[[Pasted image 20241115131639.png]]
