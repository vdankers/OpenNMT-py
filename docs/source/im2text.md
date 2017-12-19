# Example: Image to Text

A deep learning-based approach to learning the image-to-text conversion, built on top of the <a href="http://opennmt.net/">OpenNMT</a> system. It is completely data-driven, hence can be used for a variety of image-to-text problems, such as image captioning, optical character recognition and LaTeX decompilation. 

Take LaTeX decompilation as an example, given a formula image:

<p align="center"><img src="http://lstm.seas.harvard.edu/latex/results/website/images/119b93a445-orig.png"></p>

The goal is to infer the LaTeX source that can be compiled to such an image:

```
 d s _ { 1 1 } ^ { 2 } = d x ^ { + } d x ^ { - } + l _ { p } ^ { 9 } \frac { p _ { - } } { r ^ { 7 } } \delta ( x ^ { - } ) d x ^ { - } d x ^ { - } + d x _ { 1 } ^ { 2 } + \; \cdots \; + d x _ { 9 } ^ { 2 } 
```

The paper [[What You Get Is What You See: A Visual Markup Decompiler]](https://arxiv.org/pdf/1609.04938.pdf) provides more technical details of this model.

### Dependencies

* `torchvision`: `conda install torchvision`
* `Pillow`: `pip install Pillow`

### Quick Start

To get started, we provide a toy Math-to-LaTex example. We assume that the working directory is `OpenNMT-py` throughout this document.

Im2Text consists of four commands:

0) Download the data.

```
wget -O data/im2text.tgz http://lstm.seas.harvard.edu/latex/im2text_small.tgz; tar zxf data/im2text.tgz -C data/
```

1) Preprocess the data.

```
python preprocess.py -data_type img -src_dir data/im2text/images/ -train_src data/im2text/src-train.txt -train_tgt data/im2text/tgt-train.txt -valid_src data/im2text/src-val.txt -valid_tgt data/im2text/tgt-val.txt -save_data data/im2text/demo
```

2) Train the model.

```
python train.py -model_type img -data data/im2text/demo -save_model demo-model -gpuid 0 -batch_size 20 -max_grad_norm 20 -learning_rate 0.1
```

3) Translate the images.

```
python translate.py -data_type img -model demo-model_acc_x_ppl_x_e13.pt -src_dir data/im2text/images -src data/im2text/src-test.txt -output pred.txt -gpu 0 -verbose
```

The above dataset is sampled from the [im2latex-100k-dataset](http://lstm.seas.harvard.edu/latex/im2text.tgz). We provide a trained model [[link]](http://lstm.seas.harvard.edu/latex/py-model.pt) on this dataset.

### Options

* `-src_dir`: The directory containing the images.

* `-train_tgt`: The file storing the tokenized labels, one label per line. It shall look like:
```
<label0_token0> <label0_token1> ... <label0_tokenN0>
<label1_token0> <label1_token1> ... <label1_tokenN1>
<label2_token0> <label2_token1> ... <label2_tokenN2>
...
```

* `-train_src`: The file storing the paths of the images (relative to `src_dir`).
```
<image0_path>
<image1_path>
<image2_path>
...
```