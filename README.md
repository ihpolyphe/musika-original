# Installation
[公式README.md](./README_MUSIKA_ORIGINAL.md)を参照のこと。また、以下の記事にインストール方法を記載しているので参照のこと。

- [WSL2でmusikaでfinetuning](https://zenn.dev/ihpolyphe/articles/4c195a6fa343d7)

# Execution
セットアップが完了している場合、`conda activate musika`から仮想環境を起動する。

### fineturning
適当なwav ファイルを持ってきて以下でAutoencoding
```
python musika_encode.py --files_path folder_of_audio_files --save_path folder_of_encodings --whole True
```

上記で出力した`npy`を入力として以下でfineturning実行。

学習させるファイルは英語でスペースなどはないようにすること。
```
python musika_train.py --train_path folder_of_encodings --load_path checkpoints/misc --lr 0.00004
```

ちなみにVAE的にAutoEncoding後、以下で曲をdecodingできる。
```
python musika_decode.py --files_path folder_of_encodings --save_path folder_of_audio_files
```

# Other
## GPU setup tips
### cuda path
以下で通したら認識した
```
LD_LIBRARY_PATH="$LD_LIBRARY_PATH":/usr/local/cuda-11/lib64
PATH="$PATH":/usr/local/cuda/bin
```

参考URLは以下。
- https://zenn.dev/ylabo0717/articles/48796b7f3470c7
- https://github.com/marcoppasini/musika
```
export XLA_FLAGS=--xla_gpu_cuda_data_dir=/mnt/c/"Program Files"/"NVIDIA GPU Computing Toolkit"/CUDA/v11.2/nvvm/libdevice
```

## Download mp3 from YouTube
- yt-dlp -x --audio-format mp3 https://www.youtube.com/watch?v=ZRtdQ81jPUQ

musika仮想環境ではなく、ホストWSL環境で以下実行すること
```
 yt-dlp -x --audio-format wav https://www.youtube.com/watch?v=yyFr4vOCIME
```
https://zenn.dev/robes/articles/c31dc875ae29c3


## TrableShorting

### libdevice export path
```
  File "/home/user/anaconda3/envs/musika/lib/python3.9/site-packages/tensorflow/python/eager/execute.py", line 54, in quick_execute
    tensors = pywrap_tfe.TFE_Py_Execute(ctx._handle, device_name, op_name,
tensorflow.python.framework.errors_impl.InternalError: libdevice not found at ./libdevice.10.bc [Op:__inference_train_tot_23468]
```
https://github.com/tensorflow/tensorflow/issues/58681

以下を実行
```
mkdir -p $CONDA_PREFIX/lib/nvvm/libdevice/
cp -p $CONDA_PREFIX/lib/libdevice.10.bc $CONDA_PREFIX/lib/nvvm/libdevice/
export XLA_FLAGS=--xla_gpu_cuda_data_dir=$CONDA_PREFIX/lib
↑毎回実行する必要あり
conda install -c conda-forge cudatoolkit-dev=11.2 --yes
```
```
cd /home/user/miniconda3/lib/
 1407  ls
 1408  ls | grep libdevice
 1409  cd ..
 1410  cd lib
 1411  mkdir nvvm/libdevice
 1412  mkdir nvvm
 1413  cd nvvm/
 1414  mkdi libdevice
 1415  mkdir libdevice
 1416  cd libdevice/
 1417  cd /mnt/c/"Program Files"/"NVIDIA GPU Computing Toolkit"/CUDA/v11.2/nvvm/libdevice
 1418  ls
 1419  cp libdevice.10.bc /home/user/miniconda3/lib/nvvm/libdevice/
 1420  export TF_XLA_FLAGS=-–xla_gpu_cuda_data_dir=/home/user/miniconda3/lib
 /home/user/miniconda3/lib/nvvm/libdevice
 ```