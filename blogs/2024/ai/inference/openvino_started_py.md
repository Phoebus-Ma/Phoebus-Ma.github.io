
# Intel OpenVINO Started Tutorial (Python)

# 1. Create enviorment

Platform: x86_64 Ubuntu 20.04.

```bash
$ sudo apt install python3 python3-distutils libpython3.8 python3-pip python3-venv
$ python3 -m venv openvino_env
$ source openvino/bin/activate
```

openvino virtual enviorment:

```bash
(openvino_env) $ python --version
Python 3.8.10

(openvino_env) $ python -m pip install --upgrade pip
(openvino_env) $ pip install openvino==2024.0.0
(openvino_env) $ python -c "from openvino import Core; print(Core().available_devices)"
```

# 2. Download Openvino and example

install git:

```bash
$ sudo apt install git
$ git config --global user.name <your_name>
$ git config --global user.email <your_email>
```

download openvino and example:

```bash
$ git clone https://github.com/openvinotoolkit/openvino.git --depth=1
$ git clone https://github.com/openvinotoolkit/open_model_zoo.git --depth=1
```


# 3. Benchmark

```bash
$ mkdir local_models
$ python3 open_model_zoo/tools/model_tools/downloader.py \
    --name face-detection-0206 -o local_models

$ python3 openvino/samples/python/benchmark/sync_benchmark/sync_benchmark.py \
    local_models/intel/face-detection-0206/FP16/face-detection-0206.xml
```


# 4. Download onnx model and convert model

```bash
$ python3 open_model_zoo/tools/model_tools/downloader.py \
    --list open_model_zoo/demos/image_inpainting_demo/python/models.lst

$ python3 open_model_zoo/tools/model_tools/converter.py \
    --list open_model_zoo/demos/image_inpainting_demo/python/models.lst

$ python3 open_model_zoo/demos/image_inpainting_demo/python/image_inpainting_demo.py \
    -m open_model_zoo/demos/image_inpainting_demo/python/public/gmcnn-places2-tf/FP16/gmcnn-places2-tf.xml \
    -i open_model_zoo/demos/image_inpainting_demo/python/car_1_example.png -d CPU -ar
```
