FROM python:3.8

RUN apt-get update && \
    apt-get install -y git python3-opencv unzip

# setup py-bottom-up attention
WORKDIR /dependencies
RUN git clone https://github.com/airsplay/py-bottom-up-attention.git && \
    cd py-bottom-up-attention && \
    pip install torch==1.8.1+cpu torchvision==0.9.1+cpu torchaudio==0.8.1 -f https://download.pytorch.org/whl/torch_stable.html && \
    pip install numpy cython opencv-python && \
    pip install 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI' && \
    pip install 'git+https://github.com/facebookresearch/fvcore.git' && \
    python setup.py build develop

# setup oscar
WORKDIR /dependencies
RUN git clone --recursive https://github.com/eugenekoh/Oscar.git && \
    cd Oscar && \
    git checkout sequence-coco && \
    python setup.py build develop && \
    pip install tqdm pyyaml matplotlib requests scikit-image anytree regex boto3 tensorboardX torch_optimizer

# actual project
WORKDIR /code
RUN git clone https://github.com/eugenekoh/librephotos-serve.git . && \
    pip install pytorchcv loguru flask pillow && \
    wget https://storage.googleapis.com/ntu-rcp/model.tar.gz && \
    tar -xf model.tar.gz -C pic && \
    wget https://biglmdiag.blob.core.windows.net/oscar/exp/coco_caption/base/checkpoint.zip && \
    unzip checkpoint.zip -d sic

EXPOSE 5000
CMD ["python", "app.py"]
