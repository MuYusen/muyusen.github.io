---
layout: mypost
title: Python识别网站验证码：pytesseract
categories: [pytesseract]
---

pyteeseract 识别功能是基于 tesseract 开源工具完成的，所以需要先 安装 tesseract。

```bash
sudo add-apt-repository ppa:alex-p/tesseract-ocr-devel
sudo apt update

sudo apt install tesseract-ocr
sudo apt install libtesseract-dev
```

+ https://tesseract-ocr.github.io/
+ https://launchpad.net/~alex-p/+archive/ubuntu/tesseract-ocr-devel

然后通过 pip 工具安装 ：pip install pytesseract

![pytesseract](2023-09-06_14-11.png)

在 pytesseract 的安装脚本(`/usr/local/lib/python3.10/dist-packages/pytesseract/pytesseract.py`)中可以，看到 tesseract 的命令执行路径(`tesseract_cmd = 'tesseract'`) 。
