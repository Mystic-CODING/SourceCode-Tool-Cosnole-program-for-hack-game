/*Assault Cube 1.3.0.2 offset, Basic menu for educational purposes only; youtube Mystic-CODING (lost-account -_-)
unknowscheats*/


#include <Windows.h>
#include <TlHelp32.h>
#include <vector>
#include <tchar.h>
#include <iostream>
#include <stdlib.h>
using namespace std;

#pragma offset

//memory address
#define iViewMatrix = 0x017DFFC - 0x6C
#define iLocalPlayer = 0x0018AC00
#define iEntityList = 0x00191FCC

//memory vector to add whit adress
#define vHead = 0x4
#define vFeet = 0x28
#define vAngles = 0x34
#define iHealth = 0xEC
#define iDead = 0xA0
#define sName = 0x205
#define iTeam = 0x30C


//offset i will use here
//--all of this address caught from localPlayer list on (cheatEngine)--//

/********************************************************************************************/
//These are individual memory addresses (base)(static).
DWORD ammoAddr = 0x00084DCC;
DWORD localPlayerAddr = 0x0018AC00;
DWORD grenadeAddr = 0x0016F338;
DWORD nameAddr = 0x0018AC00;
//These are values that are added to a base address to obtain a new address.
vector<DWORD> ammoOffset{ 0x150, 0xA94, 0x140 };
vector<DWORD> healtOffset{ 0xEC };
vector<DWORD> grenadeOffset{ 0x8, 0xA8, 0x144 };
vector<DWORD> nameOffset{ 0x205 };


/*
EXAMPLE

DWORD baseAddress = 0x0018AC00;
vector<DWORD> ammoOffset{ 0x150, 0xA94, 0x140 };
DWORD finalAddress1 = baseAddress + ammoOffset;
        ^
        |
  we will use this
*/
/********************************************************************************************/


DWORD GetModuleBaseAddress(TCHAR* lpszModuleName, DWORD pID) {
    DWORD dwModuleBaseAddress = 0;
    HANDLE hSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPMODULE, pID); // make snapshot of all modules within process
    MODULEENTRY32 ModuleEntry32 = { 0 };
    ModuleEntry32.dwSize = sizeof(MODULEENTRY32);

    if (Module32First(hSnapshot, &ModuleEntry32)) //store first Module in ModuleEntry32
    {
        do {
            if (_tcscmp(ModuleEntry32.szModule, lpszModuleName) == 0) // if Found Module matches Module we look for -> done!
            {
                dwModuleBaseAddress = (DWORD)ModuleEntry32.modBaseAddr;
                break;
            }
        } while (Module32Next(hSnapshot, &ModuleEntry32)); // go through Module entries in Snapshot and store in ModuleEntry32


    }
    CloseHandle(hSnapshot);
    return dwModuleBaseAddress;
}
DWORD GetPointerAddress(HWND hwnd, DWORD gameBaseAddr, DWORD address, vector<DWORD> offsets)
{
    DWORD pID = NULL; // Game process ID
    GetWindowThreadProcessId(hwnd, &pID);
    HANDLE phandle = NULL;
    phandle = OpenProcess(PROCESS_ALL_ACCESS, FALSE, pID);
    if (phandle == INVALID_HANDLE_VALUE || phandle == NULL);

    DWORD offset_null = NULL;
    ReadProcessMemory(phandle, (LPVOID*)(gameBaseAddr + address), &offset_null, sizeof(offset_null), 0);
    DWORD pointeraddress = offset_null; // the address we need
    for (int i = 0; i < offsets.size() - 1; i++) // we dont want to change the last offset value so we do -1
    {
        ReadProcessMemory(phandle, (LPVOID*)(pointeraddress + offsets.at(i)), &pointeraddress, sizeof(pointeraddress), 0);
    }
    return pointeraddress += offsets.at(offsets.size() - 1); // adding the last offset
}

//
int main() {
    system("Color 5"); //color of backgroud
    
    // HWND Window = FindWindowA(NULL, "AssaultCube");
    HWND Window = FindWindowA(NULL, "AssaultCube");// Find the window associated with the game "AssaultCube".

    // Check if the window was found
    if (!Window) // If the window is not found, print an error message and exit the program.
    {
        cout << "Window not found!" << endl;
        system("pause");
        exit(-1);
    }
    else // If the window is found, print a message indicating the game was found.
    {
        cout << "Game windos Foun :) \n";
    }


    DWORD Process;// Declare a variable to store the process ID.
    GetWindowThreadProcessId(Window, &Process);// Get the process ID associated with the window

    HANDLE Handler = OpenProcess(PROCESS_ALL_ACCESS, false, Process);// Open a handle to the process with full access rights.
    // Check if the process handle was obtained successfully
    if (!Handler)// If the process handle is not obtained, print an error message and exit the program.
    {
        cout << "Process not found!" << endl;
        system("pause");
        exit(-1);
    }
    char gamemoudule[] = "ac_client.exe";// Declare an array to store the game module name.
    DWORD gamebaseAddress = GetModuleBaseAddress(_T(gamemoudule), Process);// Get the base address of the game module in the process's memory space.


    //Here we sum the base address with the other offsets as described earlier.
    DWORD ammoPointer = GetPointerAddress(Window, gamebaseAddress, ammoAddr, ammoOffset);
    DWORD healtPointer = GetPointerAddress(Window, gamebaseAddress, localPlayerAddr, healtOffset);
    DWORD grenadePointer = GetPointerAddress(Window, gamebaseAddress, grenadeAddr, grenadeOffset);
    DWORD namePointer = GetPointerAddress(Window, gamebaseAddress, nameAddr, nameOffset);
    //we will use this DWORD next

    bool quit = false;
    char choise;
    int healtAmmount = 99999999;
    int ammoAmmount = 99999999;
    int grenadeAmmount = 99999999;
    string newName = "";
    do
    {
        cout << "===================\n";
        cout << "[1] God Mode    \n";
        cout << "[2] Max Ammo    \n";
        cout << "[3] max Grenade \n";
        cout << "[4] Change name \n";
        cout << "[5] INFINITE ALL 'non usable menu function again....'\n";
        cout << "[6] QUIT \n";
        cout << "===================\n";
        cin >> choise;
  
        switch (choise)
        {
        case '1':   
            WriteProcessMemory(Handler, (LPVOID)healtPointer, &healtAmmount, 4, 0); // This line writes the value of 'healtAmmount' (representing the health amount) to the memory address specified by 'healtPointer' in the target process.
            cout << "SET!";                                                         // Handler: Handle to the target process obtained using OpenProcess.
            Sleep(500);                                                             // (LPVOID)healtPointer: Casts 'healtPointer' to a pointer type suitable for WriteProcessMemory.
            break;                                                                  // &healtAmmount: Address of the variable containing the health amount to be written into the target process.
                                                                                    // 4: Number of bytes to write, assuming 'healtAmmount' is a DWORD (4 bytes). <- thivalue you see when search for offset on cheat Engine so i hope you cosnider to learn use it ;)
                                                                                    // 0: Unused parameter for the number of bytes actually written (can be NULL in this case).                                                                  
        case '2':
            WriteProcessMemory(Handler, (LPVOID)ammoPointer, &ammoAmmount, 4, 0);
            cout << "SET!";
            break;
        case '3':
            WriteProcessMemory(Handler, (LPVOID)grenadePointer, &grenadeAmmount, 4, 0);
            break;
        case '4':
            cout << "Write your new name to set \n";
            cin >> newName;
            WriteProcessMemory(Handler, (LPVOID)namePointer, newName.c_str(), newName.size() + 1, NULL);// newName.c_str(): Retrieves a pointer to the null-terminated character array of 'newName'.
            break;                                                                                      // newName.size() + 1: Total number of bytes to write, including the null terminator.
                                                                                                        // NULL: Unused parameter for the number of bytes actually written (can be NULL in this case).
        case '5':
            while (true) // Infinite loop where the variables are reinitialized, hence not changing.
            {
                //use integer like the game do for this variabiles
                int freeze_ammo = 1;
                int value_healt = 1;
                int freeze_grenade = 1;
                WriteProcessMemory(Handler, (LPVOID)ammoPointer, &freeze_ammo, 4, 0);
                WriteProcessMemory(Handler, (LPVOID)healtPointer, &value_healt, 4, 0);
                WriteProcessMemory(Handler, (LPVOID)grenadePointer, &freeze_grenade, 4, 0);
            }
            break;
        case '6':
            cout << "Quitting...";
            quit = true;
            break;
        default:
            cout << "You select no-support choise...wait ";
            Sleep(1000);
            break;
        }

        system("cls");//clear the windows
    } while (quit == false);

    cout << "bye bye";
    system("pause");
    exit(-1);
}


