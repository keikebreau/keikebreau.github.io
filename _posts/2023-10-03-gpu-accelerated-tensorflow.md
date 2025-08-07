---
title: 'How to set up GPU-accelerated TensorFlow on M2 Mac and Asus ROG Zephyrus G14'
date: 2023-10-03
permalink: /posts/2023/10/gpu-accelerated-tensorflow
tags:
  - computing
  - tensorflow
  - gpu
---

Computer specs
======

| Manufacturer | Year | CPU | System memory | GPU | GPU memory | Operating system |
| --------------------- | ---- | --- | ------------- | --- | ---------- |
| [Asus ROG Zephyrus G14](https://rog.asus.com/laptops/rog-zephyrus/rog-zephyrus-g14-2022-series/) | 2022 | [AMD Ryzen 9 6900HS](https://www.amd.com/en/products/apu/amd-ryzen-9-6900hs) | 16 GB | [AMD Radeon RX 6700S GDDR](https://www.amd.com/en/products/graphics/amd-radeon-rx-6700s) | 8 GB | Windows 11 | 
| Apple Macbook Pro | 2022 | [Apple M2](https://www.apple.com/newsroom/2022/06/apple-unveils-m2-with-breakthrough-performance-and-capabilities/) | 16 GB | Apple M2 | 16 GB (?) | MacOS Ventura |

Setup instructions for Mac
======

1. Install Python 3.10 via [Homebrew](https://brew.sh): 

    ```shell
    brew install python@3.10
    ```

2. Perform the steps at ["Get started with tensorflow-metal"](https://developer.apple.com/metal/tensorflow-plugin/).

Setup instructions for Asus ROG Zephyrus G14
======

1. Download and install the latest release of Python 3.10 from python.org. I used [Python 3.10.11](https://www.python.org/ftp/python/3.10.11/python-3.10.11-amd64.exe) directly from the python.org FTP server.

2. Install [`virtualenv` via `pip`](https://virtualenv.pypa.io/en/latest/installation.html#via-pip) and create a virtual environment to contain the experiements. This is might not be strictly necessary, but I prefer a clean development environment.

    ```powershell
    pip install virtualenv
    virtualenv venv
    venv\scripts\activate.ps1
    ```

3. Update `pip`.

    ```powershell
    python -m pip install --upgrade pip
    ```

4. Install `tensorflow-cpu` and [`tensorflow-directml-plugin`](https://github.com/microsoft/tensorflow-directml-plugin):

    ```powershell
    pip install tensorflow-cpu tensorflow-directml-plugin
    ```

*Note: [DirectML](https://learn.microsoft.com/en-us/windows/ai/directml/dml-intro) allows TensorFlow to use the AMD GPU via DirectX 12. It's TensorFlow plugin apparently requires the use of the `tensorflow-cpu` plugin. No idea what happens if you use the `tensorflow` package instead.*

Appendix: test script with runtime results
======

From ["Get started with tensorflow-metal"](https://developer.apple.com/metal/tensorflow-plugin/):
```python
import tensorflow as tf

cifar = tf.keras.datasets.cifar100
(x_train, y_train), (x_test, y_test) = cifar.load_data()
model = tf.keras.applications.ResNet50(
    include_top=True,
    weights=None,
    input_shape=(32, 32, 3),
    classes=100,)

loss_fn = tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False)
model.compile(optimizer="adam", loss=loss_fn, metrics=["accuracy"])
model.fit(x_train, y_train, epochs=5, batch_size=64)
```

| Manufacturer | Processor | Total runtime |
| ------------ | --- | ------------- |
| Asus | AMD Ryzen 9 6900HS (GPU component) | 316 seconds |
| Asus | AMD Radeon RX 6700S GDDR (dedicated GPU) | 134 seconds |
| Apple | M2 GPU | 390 seconds |

---

#### Revisions:
* 2023-10-05: reference the correct Python version in the Asus ROG Zephyrus G14 guide. 