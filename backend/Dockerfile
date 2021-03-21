FROM python:3.8

# system packages installation
RUN apt update && apt install -y curl nfs-common cifs-utils libopenblas-dev libmagic1 libboost-all-dev libxrender-dev liblapack-dev git bzip2 cmake build-essential libsm6 libglib2.0-0 libgl1-mesa-glx --no-install-recommends

# pre trained models download
WORKDIR /data_models
RUN mkdir -p /data_models/places365/
RUN mkdir -p /data_models/im2txt/
RUN mkdir -p /root/.cache/torch/hub/checkpoints/
RUN curl -SL https://s3.eu-central-1.amazonaws.com/ownphotos-deploy/places365_model.tar.gz | tar -zxC /data_models/places365/
RUN curl -SL https://s3.eu-central-1.amazonaws.com/ownphotos-deploy/im2txt_model.tar.gz | tar -zxC /data_models/im2txt/
RUN curl -SL https://s3.eu-central-1.amazonaws.com/ownphotos-deploy/im2txt_data.tar.gz | tar -zxC /data_models/im2txt/
RUN curl -SL https://download.pytorch.org/models/resnet152-b121ed2d.pth -o /root/.cache/torch/hub/checkpoints/resnet152-b121ed2d.pth
RUN pip install torch==1.7.1+cpu torchvision==0.8.2+cpu -f https://download.pytorch.org/whl/torch_stable.html
RUN pip install -v --install-option="--no" --install-option="DLIB_USE_CUDA" --install-option="--no" --install-option="USE_AVX_INSTRUCTIONS" --install-option="--no" --install-option="USE_SSE4_INSTRUCTIONS" dlib

# setup py-bottom-up attention
WORKDIR /dependencies
RUN git clone https://github.com/airsplay/py-bottom-up-attention.git && \
    cd py-bottom-up-attention && \
    pip install torch torchvision numpy cython opencv-python && \
    pip install 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI' && \
    pip install 'git+https://github.com/facebookresearch/fvcore.git' && \
    python setup.py build develop

# setup oscar
WORKDIR /dependencies
RUN git clone --recursive https://github.com/eugenekoh/Oscar.git && \
    cd Oscar && \
    git checkout sequence-coco && \
    python setup.py build develop && \
    pip install -r requirements.txt && \
    pip install pytorch-transformers==1.0.0

# setup fer
RUN pip install scikit-image pytorchcv

# actual project
ARG DEBUG
WORKDIR /code
RUN git clone https://github.com/LibrePhotos/librephotos . 
RUN pip install -r requirements.txt
RUN if [ "$DEBUG" = 1 ] ; then pip install -r -U requirements.dev.txt ;  fi
RUN python -m spacy download en_core_web_sm
EXPOSE 8001

COPY entrypoint.sh /entrypoint.sh
CMD ["/entrypoint.sh"]
