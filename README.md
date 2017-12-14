# Criar CNN - Convolutional Neural Network para identificacão de cones com TensorFlow e Keras
 - Desde a instalacao do Anaconda ao Treino e Predicao da rede ( VGG16 )

## Index

<!-- Table of contents generated by http://tableofcontent.eu/ -->
- [1 - Install Anaconda 5.1](#1-install-anaconda)
- [2 - Linux Ubuntu 16](#2-linux-ubuntu-16)
- [3 - Python para Windows](#3-python-para-windows)
- [4 - Instalando o Cuda e Cudnn com suporte a GPU](#4-install-cuda-cudnn-support-gpu)
- [5 - CUDA 8.0 GA2](#5-cuda-8-ga2)
- [6 - PATH](#6-path)
- [7 - Instalando os pacotes necessários](#install-pacages)
- [8 - Iniciando o jupyter notebook](#start-jupter)
- [9 - Montando a Rede VGG 16](#create-vgg-16)


- [Saving Remission Map Images](#saving-remission-map-images)
- [Splitting the Remission Map Images](#splitting-the-remission-map-images)
- [Rules for Ground Truth Annotations on Remission Map Images](#rules-for-ground-truth-annotations-on-remission-map-images)

## 1-Install Anaconda
Acessar o site http://anaconda.com/download
Fazer o Download da versao respectiva ao seu SO e também a versao do Python

## 2-Linux Ubuntu 16
Python 3.6 https://repo.continuum.io/archive/Anaconda3-5.0.1-Linux-x86_64.sh Python 2.7 https://repo.continuum.io/archive/Anaconda2-5.0.1-Linux-x86_64.sh Abrir um terminal, acessar o local onde o arquivo foi baixado e seguir com o seguinte comando bash ~/Downloads/Anaconda3-5.0.1-Linux-x86_64.sh (versao 3.6 do Python)

## 3-Python para Windows
Python 3.6 https://repo.continuum.io/archive/Anaconda3-5.0.1-Windows-x86_64.exe Python 2.7 https://repo.continuum.io/archive/Anaconda2-5.0.1-Windows-x86_64.exe Para instalar é só dar um duplo clique no instalador e seguir

# A partir deste momento vou cobrir os passos apenas para o linux como sistema operacional

## 4-Install-CUDA-Cudnn-support-GPU
Caso queira rodar os treinamentos com recursos de GPU é necessário instalar os pacotes CUDA e Cudnn as versoes destes sao muito importantes para que tudo funcione corretamente como até o momento a ultima versao disponível do tensorflow é a 1.4 vou usar as versões de Cuda e Cudnn que a pagina recomenda como versões suportadas lembrando que a placa de video deve ser compatível com cuda e ter os drivers da nvidia instalados

## 5-CUDA 8 GA2
Para facilitar a instalacao vou usar o wget abrir um terminal acessar o local onde quer que o arquivo seja baixado /home/usuario/Downloads mkdir Cuda cd Cuda wget https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_375.26_linux-run assim que o download for concluido dar direito de execucao para o arquivo sudo chmod +x nomeArquivoBaixado sudo ./nomeArquivoBaixado mesma situacao para o patch desta versao wget https://developer.nvidia.com/compute/cuda/8.0/Prod2/patches/2/cuda_8.0.61.2_linux-run assim que o download for concluido dar direito de execucao para o arquivo sudo chmod +x nomeArquivoPatchBaixado sudo ./nomeArquivoPatchBaixado depois devemos baixar os arquivos do cuda e extrai-los wget https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v6/prod/8.0_20170307/Ubuntu16_04_x64/libcudnn6_6.0.20-1+cuda8.0_amd64-deb sudo dpkg -i nomeArquivoBaixado

## 6-Path
O proximo passo é a inclusao do caminho destes pacotes no PATH sudo nano ~/.bashrc inserir no final do arquivo as seguintes linhas export PATH=/usr/local/cuda-8.0/binPATH:+:$PATHexportLDLIBRARYPATH=/usr/local/cuda−8.0/lib64 PATH:+:$PATHexportLDLIBRARYPATH=/usr/local/cuda−8.0/lib64 {LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

sudo apt-get install libcupti-dev

## 7-Instalando os pacotes necessários
Criar um ambiente especifico para este tuto
dentro do terminal conda create -n ufes_lcad python=3.6 source activate ufes_lcad instalar os pacotes basicos que iremos trabalhar conda install keras conda install matplotlib conda install numpy conda install pandas conda install scikit-learn pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.4.0-cp36-cp36m-linux_x86_64.whl

## 8-Iniciando o Jupyter notebook










The Road Mapper module manages the *map_type* CARMEN_ROAD_MAP_v (prefix character 'r'), the data structure *road_prob* and the map server message CARMEN_MAP_SERVER_ROAD_MAP_NAME. Each cell in a road gridmap contains the following data:
```c
 typedef struct				/* Probabilities of a pixel in the road map: range(0, 0xffff) */
 {
   unsigned short off_road;		/* Probability of being off the road */
   unsigned short solid_marking;	/* Probability of being in a lane solid marking */
   unsigned short broken_marking;	/* Probability of being in a lane broken marking */
   unsigned short lane_center;		/* Probability of being at the center of a lane */
 } road_prob;
```
The following color code is used for displaying a road map:
  - ![#ffffff](data/20x20_bg_white_border_gray.png) RGB(255, 255, 255) = off the road
  - ![#ff0000](https://placehold.it/20x20/ff0000/?text=+) RGB(255, 000, 000) = solid marking
  - ![#0000ff](https://placehold.it/20x20/0000ff/?text=+) RGB(000, 000, 255) = broken marking
  - ![#00ff00](https://placehold.it/20x20/00ff00/?text=+) RGB(000, 255, 000) = center of a lane


