FROM gcr.io/andela-docker/ci-builder-python:0.0.0

RUN pip install envtpl && \
    wget https://github.com/garethr/kubeval/releases/download/0.7.3/kubeval-linux-amd64.tar.gz && \
    tar xf kubeval-linux-amd64.tar.gz && rm -rf kubeval-linux-amd64.tar.gz && \
    mv kubeval /usr/local/bin