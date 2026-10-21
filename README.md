# PS2-DevKit

A complete PlayStation 2 Development Kit(I think) with everything you need to develop homebrew applications and games(maybe) for the PS2. This package includes the SDK (though old, it works), CD/DVD Generator, and PS2Link networking tools.

Download: https://drive.google.com/drive/folders/18uXYgLyBS8E6h6izeqCzyAB1ZefBH05_?usp=sharing

PT-BR: Caso fale portugues eu tenho um canal no youtube onde ensino como instalar e testo. E também fiz videos sobre como criar icones para o memory card. Link: https://www.youtube.com/@Mr.Rafael2005

## CONTENTS
- **DOCS**
  - Conference_Presentations 
  - Guides
  - Hardware
  - PS2_Fundamentals
- **SDK (GSHI_PS2SDK 5.0):**
  - MinGW
  - MSYS (1.0)
  - Vista (MSYS for Windows Vista)
- **PS2LINK**
  - **PS2:**
    - PS2LINK.ELF (1.9.1)
    - IPCONFIG.DAT
    - PS2LINK.icn
    - icon.sys
  - **PC:**
    - fsclient.exe
    - ps2client.exe
    - pthreadGC2.dll
- **CDDVD:**
  - CDVD_GEN (2.0)
  - CDVD_GEN_PT (Portuguese version)
  - IML2ISO (5.3)
  - DUMMY (Empty)
  - SYSTEM.CNF
- **SAMPLES:**
  - Some samples of what you can do with this sdk.

With this kit, you can develop PS2 applications and, potentially, games.

---

## SETTING UP THE SDK
1. Extract the SDK.
2. Copy the `MSYS` and `MinGW` folders to `C:\`.
3. Go to the `MSYS\1.0` folder and run `msys.bat`.

In the MSYS terminal, execute:

```sh
cd $PS2DEV
cd ps2sdksrc
make install   # Installs ps2sdk
```

To get and build samples:

```sh
cd ../ps2sdk   # Enter the new sdk folder
cd samples
make           # Build all samples
```

To try a sample:

```sh
cd cube
make
# cube.elf can now be run in a PS2 Emulator
```
## If you wish, you can use MSYS2 to install the latest precompiled version of the PS2SDK.
-Download everything (MSYS2 and the pre-compiled PS2SDK at https://github.com/ps2dev/ps2dev/releases/tag/v2.0.0)
-Install MSYS2 (sometimes the setup only works when installed on the C:/ drive)
-Place the pre-compiled archive (while still compressed) into the `/home/your_username` folder within `C:/msys64`

-Add the environment variables to your `bash_profile`:
export PS2DEV=/usr/local/ps2dev
export PS2SDK=$PS2DEV/ps2sdk
export GSKIT=$PS2DEV/gsKit
export PATH=$PATH:$PS2DEV/bin:$PS2DEV/ee/bin:$PS2DEV/iop/bin:$PS2DEV/dvp/bin:$PS2SDK/bin

-Open the MinGW32 terminal
-Create the directory and extract the pre-compiled files:

```sh
mkdir -p $PS2DEV
chown -R $USER: $PS2DEV
tar -xf ps2dev-windows-latest.tar.gz --strip-components 1 -C $PS2DEV
```
(If any symlink errors occur, you can ignore them)

Install library dependencies:
```sh
pacman -S --needed make \
mingw-w64-i686-gcc \
mingw-w64-i686-gdb \
mingw-w64-i686-expat \
mingw-w64-i686-gmp \
mingw-w64-i686-mpc \
mingw-w64-i686-mpfr \
mingw-w64-i686-libiconv \
mingw-w64-i686-ncurses \
mingw-w64-i686-termcap \
mingw-w64-i686-xz \
mingw-w64-i686-zlib \
mingw-w64-i686-zstd
```
---

## SETTING UP PS2LINK (PS2 SIDE)

1. Extract the PS2LINK archive and the `PS2` folder.
2. Open CMD and run `ipconfig` to find your PC's IP and gateway.
3. Open `IPCONFIG.DAT`. It contains three values:
   - Example: `192.168.0.10 (IP) 255.255.255.0 (MASK) 192.168.0.1 (GATEWAY)`
4. Set the gateway and mask to match your PC network.
5. Choose a PS2 IP in the same range as your PC (e.g. if your PC is `192.168.3.10`, use `192.168.3.X` with `0 < X < 256` for your PS2).
6. Copy all files to a folder on your PS2 memory card. **(If you don't, IPCONFIG.DAT won't be used and default values will load. You can use LaunchELF for this.)**
7. Connect your PS2 to your PC's internet router.

---

## SETTING UP PS2LINK (PC SIDE)

1. Extract the PS2LINK archive and the `PC` folder.
2. Copy all files to `C:\msys\1.0\local\ps2dev\ps2sdk\bin` (the ps2sdk bin directory).

**To use PS2Link:**

- Go to the `texture` sample directory.
- Open its `Makefile`.
- Change the `run:` target to something like:

  ```make
  run: $(EE_BIN)
    ps2client -h PS2_IP_Here execee host:$(EE_BIN)
  ```

- Change the `reset:` target to:

  ```make
  reset:
    ps2client -h PS2_IP_Here reset
  ```

- In the MSYS terminal, navigate to the `texture` sample folder:

  ```sh
  cd $PS2DEV/ps2sdk/samples/texture
  make
  make run      # Ctrl+C to stop debug
  make reset    # Resets ps2link; now you can recompile with "make" and "make run" again
  ps2client -h PS2_IP_Here poweroff  # Powers off the PS2
  ```

> Note: Running `texture.elf` in a PS2 emulator won't work because it accesses `host:texture.raw`, but it will work on real hardware with ps2link.

---
## SETTING UP CDVD

1. **Extract the CDVD package** and install IML2ISO.
2. **Prepare the necessary files for your project:**
   - `SYSTEM.CNF` (configuration file executed by the PS2 at boot; available in the CDVD folder)
   - `DUMMY` (a file used to fill space and ensure the minimum disc size)
   - Your compiled `.elf` file, RENAMED to the correct format, e.g., `SLUS_999.99` (remove the `.elf` extension). See `DOCS/Guides/CDGEN.HTM` for naming details.
   - Other project files (assets, textures, sounds, etc.)

3. **Edit the `SYSTEM.CNF` file:**
   - Make sure the line `BOOT2 = cdrom0:\SYYY_XXX.XX;1` points to your main executable (renamed, without the `.elf` extension, and followed by `;1`).

4. **Open `cdvdgen.exe`:**
   - Choose your disc type: `DVD` (up to 4GB) or `DVD Dual` (up to 8GB).
   - Drag all your project files into the program window, **except for the `DUMMY` file at this stage**.

5. **Configure disc properties:**
   - In the "Volume" tab, set the “Disc Name” to match your main executable (`SLUS_999.99`, for example).

6. **Save the project in `.ccz` format.**

7. **Export to `.iml` file:**
   - Try exporting directly to an `.iml` file.
   - **If no error occurs, skip to step 8.**
   - **If you get an error about minimum disc size:**
     1. Go to “Options” and enable the dummy file option.
     2. Go back to “Directory” and add the `DUMMY` file.
     3. In “Layout”, find the `DUMMY` file, right-click it, and set its location to `600000` (this ensures it fills the required space).
     4. Export the project again to `.iml` file.

8. **Open IML2ISO:** 
   - Click the folder icon and select your `.iml` file.
   - Click the "iml2iso" button to generate the ISO image.
   - Note: Google Drive flagged this program as if it had a virus lol. You can also use this: https://github.com/master801/iml2iso

**Done! You now have a functional PS2 disc image to burn or use in emulators/real hardware.**

---

**Additional Tips:**
- Refer to `DOCS/Guides/CDGEN.HTM` for advanced formatting and CDVDGEN usage tips.
- The `DUMMY` file is only used to meet the PS2’s minimum disc size requirements; do not store important data in it.
---
