# Fix: Run Executable as Administrator on RAM Disk in Windows 11

In Windows 11 24H2 (including 2024 LTSC), User Account Control (UAC) fails to correctly handle `.exe` files on a RAM disk, resulting in an "Incorrect function" error when attempting to run them as an administrator.

![Incorrect_function_window](https://github.com/user-attachments/assets/46bd4ff3-cbef-472d-affc-6f5a65da6639)

This solution adds a "Run as administrator (RAM Disk Fix)" option to the right-click context menu of `.exe` files. This option opens an elevated command prompt using PowerShell, which then runs the selected executable file with administrator privileges, bypassing the UAC issue.

## How It Works

The process behind this fix is simple:

1. When right-clicking an executable file and selecting "Run as administrator (RAM Disk Fix)", a **PowerShell command** is triggered.
2. The PowerShell command launches an **elevated command prompt** (with administrator privileges).
3. The elevated command prompt then runs the selected executable file, bypassing the UAC triggered by the original file, since UAC is only triggered for `cmd.exe`, not for the executable itself.

This method avoids the "Incorrect function" error without triggering UAC for the `.exe` file on the RAM disk.

## How to Use

1. **Download and Import the Registry File**
   - Download [Win11-RAMDisk-Admin-Fix.reg](Win11-RAMDisk-Admin-Fix.reg) from the repository and double-click it to import the registry.

2. **Optional: Switch to the Old Context Menu**
   - To avoid holding the Shift key when right-clicking an `.exe` file, modify the registry by running the following command in **Command Prompt**:
     ```sh
     reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
     ```
   - To revert to the default context menu, use this command:
     ```sh
     reg.exe delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f
     ```

3. **Restart Explorer**
   - Restart **Windows Explorer** via Task Manager to apply the changes to the right-click context menu.

4. **Using the "Run as administrator (RAM Disk Fix)" Option**
   - After importing the registry file and restarting Explorer, right-click on the `.exe` file located on the RAM disk.
   - **If using the default context menu**, hold down the Shift key while right-clicking the `.exe` and select **"Run as administrator (RAM Disk Fix)"**.
   - **If using the old context menu**, simply right-click the `.exe` file and select **"Run as administrator (RAM Disk Fix)"**.

    ![runasadmin](https://github.com/user-attachments/assets/b23920bc-b5af-45f8-998f-c5117c9d5870)


## Uninstall

To uninstall the fix and remove the registry changes, run the following command in an administrator command prompt:
```sh
reg.exe delete "HKEY_CLASSES_ROOT\exefile\shell\RunAsAdminInRAMDisk" /f
```


## Important Security Warning

This method bypasses UAC checks for the selected `.exe` file, instead triggering UAC for the trusted system process `cmd.exe`. As a result, no warnings about revoked certificates (red UAC) or no certificates (yellow UAC) will appear for the executable. This behavior can be exploited by malware, allowing it to run without triggering the usual UAC warnings.

Use this method only for trusted `.exe` files, as it mitigates important security checks.

## Other

This fix has been verified to work well with **ImDisk Toolkit** (open source RAM Disk software) on Windows 11 24H2. It should also work with other RAM Disk software, but has not been tested.

Disclosure: this document was modified by ChatGPT to make it easier to read.

### Alternative Method: Running Executables as Administrator via `cmd.exe` Shortcut

**Run the Executable as Administrator by Dragging the `.exe` onto a `cmd.exe` Shortcut**

1. **Create a `cmd.exe` Shortcut**:
   - Shift-right-click on the desktop to open the context menu.
   - Select **New -> Shortcut**.
   - Enter the following content:
     ```plaintext
     %windir%\System32\cmd.exe /k start ""
     ```
   - Follow the prompts to create the shortcut.

2. **Configure the Shortcut to Run as Administrator**:
   - Shift-right-click on the newly created `cmd.exe` shortcut.
   - Select **Properties**.
   - Click on the **Shortcut** tab.
   - Click **Advanced**.
   - Check the box for **Run as administrator**.
   - Click **OK** to apply the changes.

3. **Using the Shortcut**:
   - Drag any `.exe` file onto the `cmd.exe` shortcut.
   - The executable will run with administrator privileges without causing the "Incorrect function" error, even on a RAM disk.


## License

This project is available under the [Creative Commons Zero (CC0) License](LICENSE), allowing anyone to use, modify, and distribute the work without restrictions.

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](https://creativecommons.org/publicdomain/zero/1.0/)

