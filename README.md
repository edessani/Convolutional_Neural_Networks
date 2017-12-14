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
- [7 - Instalando os pacotes](#7-instalando-pacotes)
- [8 - Iniciando o jupyter notebook](#8-iniciando-jupyter)
- [9 - Montando a Rede VGG 16](#9-montando-rede-vgg-16)

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

## 7-Instalando Pacotes
Criar um ambiente especifico para este tuto
dentro do terminal conda create -n ufes_lcad python=3.6 source activate ufes_lcad instalar os pacotes basicos que iremos trabalhar conda install keras conda install matplotlib conda install numpy conda install pandas conda install scikit-learn pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.4.0-cp36-cp36m-linux_x86_64.whl

## 8-Iniciando Jupyter
Dentro do terminal source activate ufes_lcad anaconda-navigator ou somente jupyter clicar em new -> python 3

## 9-Montando Rede VGG 16

- Importando os pacotes 
```c
import tensorflow as tf
import keras
import numpy as np
from keras.models import Sequential
from keras.layers import Conv2D
from keras.layers import MaxPooling2D
from keras.layers import Flatten
from keras.layers import Dense
from keras.layers import Dropout
from keras.optimizers import SGD
from keras.utils import multi_gpu_model
from keras.preprocessing.image import ImageDataGenerator
```

- Definindo variáveis para ajudar nos parametros de treinamento
```
width, height, batch = 96, 96, 50
sgd = SGD(lr=0.01, decay=5e-4, momentum=0.9, nesterov=True)
```

- Define que o metodo de montagem do modelo será sequencial camada por camada
```
modelo = Sequential()
```

- Construindo a primeira camada
Definimos que esta camada produzirá 64 filtros de saída, seu kernel é de 3 x 3 o stride é de 1 por 1 a entrada será de 224 por 224 colorida, portanto 3 canais e a funcão de ativacao desta camada será a ReLu 

```
modelo.add(Conv2D(filters = 64, kernel_size = (3, 3), padding= 'same', strides=(1,1), input_shape = (width, height, 3), activation = 'relu'))
```

- Seguindo sequencialmente configuramos a segunda camada, que assume como entrada a saída da camada anterior e por isso não é preciso declarar este valor, aqui ainda mantemos 4 filtros de saída, kernel de 3 x 3 e stride é de 1 por 1
```
modelo.add(Conv2D(filters = 64, kernel_size = (3, 3), padding='same', strides=(1,1), activation = 'relu'))
```

- Seguimos para a primeira camada de max Pooling, está camada tem um pool size kernel de 2 por 2 e um stride também de 2 por 2
```
modelo.add(MaxPooling2D(pool_size = (2, 2), strides=(2,2)))
```
- Com isso concluímos o primeiro bloco da VGG 16, vou considerar o fim de cada bloco sempre após uma camada de pooling
- No segundo bloco da VGG 16 há um aumento no número de saídas das camadas em 2x os outros parametros sao iguais, este aumento ocorre sempre que houver um pooling e tem fator de multiplicacao 2 limitado a 512

```
modelo.add(Conv2D(filters = 128, kernel_size = (3, 3), padding='same', strides=(1,1), activation = 'relu'))
modelo.add(Conv2D(filters = 128, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
```

- Seguimos para a segunda camada de max Pooling, está camada tem um pool size kernel de 2 por 2 e um stride também de 2 por 2
```
modelo.add(MaxPooling2D(pool_size = (2, 2), strides=(2,2)))
```

- No terceiro bloco da VGG 16 há um aumento no número de saídas das camadas em 2x e há o acrescimo de mais uma camada convolucional antes do pooling
```
modelo.add(Conv2D(filters = 256, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
modelo.add(Conv2D(filters = 256, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
modelo.add(Conv2D(filters = 256, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
```

- seguimos para a terceira camada de max Pooling, está camada tem um pool size kernel de 2 por 2 e um stride também de 2 por 2
```
modelo.add(MaxPooling2D(pool_size = (2, 2), strides=(2,2)))
```

- No quarto bloco da VGG 16 há um aumento no número de saídas das camadas em 2x os outros parametros sao iguais
```
modelo.add(Conv2D(filters = 512, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
modelo.add(Conv2D(filters = 512, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
modelo.add(Conv2D(filters = 512, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu')) 
```

- seguimos para a terceira camada de max Pooling, está camada tem um pool size kernel de 2 por 2 e um stride também de 2 por 2
```
modelo.add(MaxPooling2D(pool_size = (2, 2), strides=(2,2)))
```

- No quinto bloco da VGG 16 todos parametros sao iguais ao da quarta camada, os valores dos filtros nao sao multiplicados por 2 porque já atingimos o teto de 512

```
modelo.add(Conv2D(filters = 512, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
modelo.add(Conv2D(filters = 512, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu'))
modelo.add(Conv2D(filters = 512, kernel_size = (3, 3), padding='same', strides=(1,1), activation= 'relu')) 
```

- seguimos para a quarta camada de max Pooling, está camada tem um pool size kernel de 2 por 2 e um stride também de 2 por 2
```
modelo.add(MaxPooling2D(pool_size = (2, 2), strides=(2,2)))
```

- neste momento preparamos a saída para o formato que a entrada da primeira camada FullyConnected precisa
```
modelo.add(Flatten())
```

- Serão 3 camadas FC. A primeira tem uma entrada de tamanho de 4096 e funcao de ativacao relu
```
modelo.add(Dense(units = 4096, activation = 'relu'))
```

- logo após a primeira camada FC é feito um dropout com taxa de 50%
```
modelo.add(Dropout(0.5))
```

- A segunda camada FC tem a mesma configuracao da primeira
```
modelo.add(Dense(units = 4096, activation = 'relu'))
```

- Logo após a segunda camada FC é feito mais um dropout com taxa de 50%
```
modelo.add(Dropout(0.5))
```

- A terceira camada na rede original VGG16 tem entrada de tamanho 1000, pois a mesma classifica 1000 classes devido a nossa necessidade de classificar apenas 3 classes o meu numero de entrada para a terceira camada será de 3 a funcao de ativacao da terceira camada a softmax
```
modelo.add(Dense(units = 3, activation = 'softmax'))
```

- Antes de iniciar o treino da rede precisamos definir onde estão as fotos que serao usadas para treino e onde estao as fotos que serao usadas para teste para aumentar o numero de amostras de fotos utilzamos o recurso do keras que aumenta/diminui a foto, inverte, gira e etc
```
train_datagen = ImageDataGenerator(rescale = 1./255, shear_range = 0.2, zoom_range = 0.2, horizontal_flip = True)
test_datagen = ImageDataGenerator(rescale = 1./255)
```

 - Definimos entao onde esta o diretorio contendo o dataset separado em pastas com a seguinte hierarquia dentro da pasta dataset temos mais duas pastas com nome treino e teste dentro de cada uma das pastas de treino e teste temos a quantidade de pastas equivalente as classes no meu caso 3, ou seja, tenho 3 pastas dentro da pasta treino e mais 3 pastas dentro da pasta teste elas devem ter o mesmo nome e conter a classe que será treinada.
 - Aqui definimos também o formato que será a entrada da primeira camada de rede, o padrao de entrada para a VGG16 224 x 224 x 3, o batch padrao da VGG16 é de 256

```
training_set = train_datagen.flow_from_directory('dataset/training_set', target_size = (width, height), batch_size = batch, class_mode = 'categorical')
test_set = test_datagen.flow_from_directory('dataset/test_set', target_size = (width, height), batch_size = batch, class_mode = 'categorical')
```

- Antes de iniciar o treino podemos exibir como está montada a rede
```
modelo.summary()
```

- Podemos agora iniciar o treinamento da nossa rede, aqui ainda definimos o numero de passos por treino e validacao e tambem a quantidade de epochs
```
modelo.fit_generator(training_set, steps_per_epoch = 8591, epochs = 15, validation_data = test_set, validation_steps = 2200,  use_multiprocessing=True)

from keras.preprocessing import image
test_image = image.load_img('dataset/single_prediction/cat_or_dog_2.jpg', target_size = (96, 96))
test_image = image.img_to_array(test_image)
test_image = np.expand_dims(test_image, axis = 0)
result = modelo.predict(test_image)
print(result)
training_set.class_indices

```

# Save model pesos
```
modelo.save('VGG16.h5')
print("Model saved")    
```

# Save model estrutura
```
modelo_json = modelo.to_json()
with open("VGG16Estrutura.json", "w") as json_file:
    json_file.write(modelo_json)
 ```
