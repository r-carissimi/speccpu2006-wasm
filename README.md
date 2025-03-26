# SPECCPU2006 on WebAssembly (WASM)

This repository provides a setup to compile and run SPEC CPU 2006 benchmarks on WebAssembly (WASM) with WASI. The steps have been tested on **Ubuntu 20.04 Server**.

## Prerequisites

Ensure your system is up to date and has the necessary dependencies installed.

#### 1. Install System Dependencies

```sh
sudo apt update && sudo apt upgrade -y
sudo apt install gcc make -y
```

#### 2. Install [Wasmtime](https://docs.wasmtime.dev/cli-install.html)

```sh
curl https://wasmtime.dev/install.sh -sSf | bash
```

Create a symbolic link and verify installation:
```sh
sudo ln -s ~/.wasmtime/bin/wasmtime /usr/bin/wasmtime
wasmtime --version
```

#### 3. Install [WASI SDK](https://github.com/WebAssembly/wasi-sdk)
Adjust the version number as needed:
```sh
wget https://github.com/WebAssembly/wasi-sdk/releases/download/wasi-sdk-25/wasi-sdk-25.0-x86_64-linux.deb
sudo apt install ./wasi-sdk-25.0-x86_64-linux.deb
```

Verify installation:
```sh
/opt/wasi-sdk/bin/clang -v
```

## Setting Up SPEC CPU 2006

#### 4. Obtain SPEC CPU 2006
You must have a licensed copy of **SPEC CPU 2006**. Place it in your desired directory (e.g., `~/speccpu/`). This repository does not provide the benchmark files as they are **paid software** 😢.

#### 5. Install SPEC CPU 2006
Navigate to the benchmark directory and run the following. **You have to source the file every time you open a new terminal**. 
```sh
cd ~/speccpu/
./install.sh
. ./shrc  # Source the environment file
```

## Applying the WASM Patch

#### 6. Clone This Repository
```sh
git clone https://github.com/r-carissimi/speccpu2006-wasm.git
```

#### 7. Apply the Patch
```sh
patch -ruN -p1 < ../speccpu2006-wasm/speccpu-2006.patch
```

## Building `specinvoke` for WASM

#### 8. Build and Install the patched `specinvoke`
```sh
cd tools/src/specinvoke
./configure
make
/usr/bin/install -c specinvoke ../../../bin/
/usr/bin/install -c specinvoke_pm ../../../bin/
packagetools wasm-wasi
cd ../../../
```

## Running the Benchmarks

#### 9. Run the WASM-Compatible Benchmarks
Run the benchmarks with the **test dataset**:
```sh
runspec --config=wasm.cfg --size=test --noreportable --tune=base --iterations=1 bzip2 mcf milc namd libquantum lbm astar gcc
```

To run the **full benchmark**, remove `--size=test`. Note that this will take significantly longer.

## Supported Benchmarks

The following SPEC CPU 2006 benchmarks successfully compile and run on WASM/WASI:

- `bzip2`
- `mcf`
- `milc`
- `namd`
- `libquantum`
- `lbm`
- `astar`

Additionally, `gcc` compiles but does not execute due to missing **exception handling support** in Wasmtime.

## Additional Resources
- [SPEC CPU 2006 Documentation](https://www.spec.org/cpu2006/docs/)
- [WASI SDK GitHub](https://github.com/WebAssembly/wasi-sdk)
- [Wasmtime Documentation](https://docs.wasmtime.dev/)

## Contributing

Contributions are welcome! If you find any issues or have improvements, please submit a pull request or open an issue.

## License

This project is licensed under the GNU General Public License v3.0.

