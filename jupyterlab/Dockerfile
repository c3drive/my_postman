FROM ubuntu:latest

RUN apt-get update && apt-get install -y \
	sudo \
	wget \
	vim
WORKDIR /opt
RUN wget https://repo.continuum.io/archive/Anaconda3-2019.10-Linux-x86_64.sh && \
	sh /opt/Anaconda3-2019.10-Linux-x86_64.sh -b -p /opt/anaconda3 && \
	rm -f /opt/Anaconda3-2019.10-Linux-x86_64.sh
ENV PATH /opt/anaconda3/bin:$PATH

# Pythonのpakage管理ツール
RUN pip install --upgrade pip
RUN pip install boto3

# 構成図作画
RUN pip install diagrams
RUN apt install -y graphviz

WORKDIR /
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--allow-root", "--LabApp.token=''"]