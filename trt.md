# TensorRT

## Setup and start Tensorflow2 docker

```bash
sudo apt install nvidia-docker2
docker pull nvcr.io/nvidia/tensorflow:22.04-tf2-py3
docker run --gpus all -it nvcr.io/nvidia/tensorflow:22.04-tf2-py3
```

Once in the docker, install `tf2onnx`.

```bash
pip install git+https://github.com/onnx/tensorflow-onnx
```

Save the container as a new image `tf2trt`.

```bash
docker ps -a
docker commit <container ID> tf2trt
```

Start the docker in the folder with input models.

```bash
docker run --gpus all --ipc=host -it --rm -v $PWD:/workspace/models tf2trt
```

## TF->ONNX

Use `Keras` H5 input files.

```bash
python -m tf2onnx.convert --keras <input>.h5 --output <output>.onnx --opset 13
```

You can change the input and output layer names by using this, but will have to be specified to `trtexec`.

```bash
--rename-inputs permute_input --rename-outputs logits
```

## ONNX->TRT

1024 is the batch size.  
Alternative is `polygraphy`, but it sometimes crashes.  
`--shapes` allows us to fix the batch size, which is by default dynamic `?x64`, seen in `Netron`.  
`--dumpProfile` gives layer performance.

```bash
trtexec --onnx=<input>.onnx --saveEngine=<output>.plan --fp16 --shapes=input_1:0:1024x64 --dumpProfile
```

If necessary, change the batch size.
Example here is a 4096->8192 batch size change.

```bash
polygraphy surgeon sanitize <input>.onnx --override-input-shapes permute_input:[1,1528,3,8192] -o <output>.onnx
```

Inspect the model using polygraphy.

```bash
polygraphy inspect model <input>.plan
```
