# Nano ggml

**This is Nano ggml, a lightweight framework designed for mobile, watches, and IoT devices. It’s based on GGML, with minimal code and <u>_no third-party libraries._</u>**

- [Introduction to ggml](https://huggingface.co/blog/introduction-to-ggml)
- [The GGUF file format](https://github.com/ggerganov/ggml/blob/master/docs/gguf.md)


## Features

- **_No third-party dependencies_**
- Integer quantization support
- Broad hardware support
- Automatic differentiation
- ADAM and L-BFGS optimizers
- Zero memory allocations during runtime

## Build

```bash
git clone git@github.com:manyuanbin/ggml-on-device.git
cd ggml-on-device

# install python dependencies in a virtual environment
python3.10 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# build the examples
mkdir build && cd build
cmake ..
cmake --build . --config Release -j 8
```

## GPT-2 inference

```bash
# run the GPT-2 small 117M model with q8
../apps/gpt-2/download-ggml-model.sh 117M
./bin/gpt-2-backend -m models/gpt-2-117M/ggml-model-q8_0.bin -p "This is an example"
```

## Model Quantization

**How do you quantize a model? Reach out to  [Yuanbin](mailto:ybinman@bu.edu)!**

```
240M	models/gpt-2-117M/ggml-model-f16.bin
 70M	models/gpt-2-117M/ggml-model-q4_0.bin
 78M	models/gpt-2-117M/ggml-model-q4_1.bin
 70M	models/gpt-2-117M/ggml-model-q4_k.bin
129M	models/gpt-2-117M/ggml-model-q8_0.bin
240M	models/gpt-2-117M/ggml-model.bin
```


## Compiling for Android

Download and unzip the NDK from this download [page](https://developer.android.com/ndk/downloads). Set the NDK_ROOT_PATH environment variable or provide the absolute path to the CMAKE_ANDROID_NDK in the command below.

```bash
cmake .. \
   -DCMAKE_SYSTEM_NAME=Android \
   -DCMAKE_SYSTEM_VERSION=33 \
   -DCMAKE_ANDROID_ARCH_ABI=arm64-v8a \
   -DCMAKE_ANDROID_NDK=$NDK_ROOT_PATH
   -DCMAKE_ANDROID_STL_TYPE=c++_shared
```

```bash
# create directories
adb shell 'mkdir /data/local/tmp/bin'
adb shell 'mkdir /data/local/tmp/models'

# push the compiled binaries to the folder
adb push bin/* /data/local/tmp/bin/

# push the ggml library
adb push src/libggml.so /data/local/tmp/

# push model files
adb push models/gpt-2-117M/ggml-model.bin /data/local/tmp/models/

adb shell
cd /data/local/tmp
export LD_LIBRARY_PATH=/data/local/tmp
./bin/gpt-2-backend -m models/ggml-model.bin -p "this is an example"
```

