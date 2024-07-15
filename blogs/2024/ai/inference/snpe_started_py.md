
# Qualcomm SNPE AI Tutorial

# 1. Platform

- Windows 11 on x64
- Powershell
- Visual Studio 2022
- Python 3.10
- Qualcomm Neural Processing SDK


# 2. Set enviorment

```bash
$ py -3.10 -m venv "<PYTHON3.10_VENV_ROOT>"
$ & "<PYTHON3.10_VENV_ROOT>\Scripts\Activate.ps1"
$ python -m pip install --upgrade pip
```

Please confirm where you actually installed SNPE:

```bash
$ & "C:\Qualcomm\AIStack\QAIRT\2.24.0.240626\bin\envsetup.ps1"
$ python "${SNPE_ROOT}\bin\check-python-dependency"
$ pip install tensorflow==2.10.1
$ python -c "import tensorflow"
```

```bash
$ & "${SNPE_ROOT}/bin/check-windows-dependency.ps1"
$ & "${SNPE_ROOT}/bin/envcheck.ps1" -m
```


# 3. Model Conversion

```bash
$ Set-Variable -Name "TENSORFLOW_HOME" -Value "<PYTHON3.10_VENV_ROOT>\Lib\site-packages\tensorflow\core"
$ python $SNPE_ROOT/examples/Models/InceptionV3/scripts/setup_inceptionv3_snpe.py -a .\tmpdir -d
```

```bash
$ python $SNPE_ROOT/bin/x86_64-windows-msvc/snpe-tensorflow-to-dlc \
        --input_network $SNPE_ROOT/examples/Models/InceptionV3/tensorflow/inception_v3_2016_08_28_frozen.pb \
        --input_dim input "1,299,299,3" \
        --out_node "InceptionV3/Predictions/Reshape_1" \
        --output_path inception_v3.dlc
```


# 4. Running

```bash
$ cp inception_v3.dlc $SNPE_ROOT/examples/Models/InceptionV3
$ cd $SNPE_ROOT/examples/Models/InceptionV3
$ $SNPE_ROOT\bin\x86_64-windows-msvc\snpe-net-run.exe --container inception_v3.dlc --input_list data/cropped/raw_list.txt
```

```bash
$ python scripts/show_inceptionv3_classifications_snpe.py -i data/cropped/raw_list.txt -o output/ -l data/imagenet_slim_labels.txt
Classification results
InceptionV3\data\cropped\chairs.raw      0.380809 832 studio couch
InceptionV3\data\cropped\notice_sign.raw 0.130223 459 brass
InceptionV3\data\cropped\plastic_cup.raw 0.989595 648 measuring cup
InceptionV3\data\cropped\trash_bin.raw   0.719758 413 ashcan
```
