FROM python:3.8

RUN apt-get update && \
    apt-get install -y git python3-opencv unzip

# setup py-bottom-up attention
WORKDIR /dependencies
RUN git clone https://github.com/airsplay/py-bottom-up-attention.git && \
    cd py-bottom-up-attention && \
    pip install torch torchvision numpy cython opencv-python && \
    pip install 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI' && \
    pip install 'git+https://github.com/facebookresearch/fvcore.git' && \
    python setup.py build develop

# install apex
WORKDIR /dependencies
RUN git clone https://github.com/NVIDIA/apex.git && \
    cd apex && \
    python setup.py install --cuda_ext --cpp_ext

# setup oscar
WORKDIR /dependencies
RUN git clone --recursive https://github.com/eugenekoh/Oscar.git && \
    cd Oscar && \
    git checkout sequence-coco && \
    python setup.py build develop && \
    pip install -r requirements.txt

# actual project
WORKDIR /code
RUN git clone https://github.com/eugenekoh/librephotos-serve.git && \
    cd librephotos-serve && \
    pip install -r requirements.txt && \
    wget https://storage.googleapis.com/ntu-rcp/model.tar.gz && \
    tar -xf model.tar.gz -C pic && \
    wget https://biglmdiag.blob.core.windows.net/oscar/exp/coco_caption/base/checkpoint.zip && \
    unzip checkpoint.zip -d sic

EXPOSE 5000
CMD ["python", "app.py"]
