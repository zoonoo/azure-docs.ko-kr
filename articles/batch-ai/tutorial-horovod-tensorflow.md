---
title: 자습서 - Azure Batch AI 및 Horovod를 사용한 분산 학습 | Microsoft Docs
description: 자습서 - Azure Batch AI 서비스 및 Azure CLI를 사용하여 Horovod로 분산 모델을 학습시키는 방법입니다.
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 45255845d8645391ee33471830ac2ef27870a40d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408632"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>자습서: Horovod를 사용하여 분산 모델 학습

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

이 자습서에서는 Batch AI 클러스터의 여러 노드에서 병렬로 실행하여 분산 Deep Learning 모델을 학습합니다. Batch AI는 Azure GPU에서 기계 학습 및 AI 모델을 대규모로 학습하기 위한 관리 서비스입니다. 

이 자습서에서는 Azure CLI를 통해 Batch AI 리소스와 상호 작용하는 방법과 일반적인 Batch AI 워크플로를 소개합니다. 다음 내용을 다룹니다.

> [!div class="checklist"]
> * Batch AI 작업 영역, 실험 및 클러스터 설정
> * 입/출력용 Azure 파일 공유 설정
> * Horovod를 사용하여 딥러닝 모델 병렬 처리
> * 학습 작업 제출
> * 작업 모니터링
> * 학습 결과 검색

이 자습서에서는 [Horovod](https://github.com/uber/horovod)를 사용하여 개체 감지 모델이 병렬로 실행되도록 수정합니다. 모델은 [CIFAR-10 데이터 세트](https://www.cs.toronto.edu/~kriz/cifar.html) 이미지에서 학습됩니다. 학습 작업은 24개의 vCPU 및 4개의 GPU를 포함하는 클러스터에서 실행되고 완료하는 데 약 60분이 걸립니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.38 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="why-use-horovod"></a>Horovod를 사용하는 이유

Horovod는 Tensorflow, Keras 및 PyTorch용 분산 학습 프레임워크로, 이 자습서에 사용됩니다. Horovod를 사용하면 단 몇 줄의 코드만으로 단일 GPU에서 실행되도록 디자인된 학습 스크립트를 분산 시스템에서 실행되는 스크립트로 변환해 효율적인 사용이 가능합니다.

Batch AI는 Horovod 외에도 자주 쓰이는 다른 오픈 소스 프레임워크를 사용하여 분산 학습을 지원할 수 있습니다. 프로덕션 환경에서 모델을 학습하는 데 사용하는 프레임워크의 사용 조건을 검토해야 합니다.

## <a name="prepare-the-batch-ai-environment"></a>Batch AI 환경 준비

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

`az group create` 명령을 사용하여 `eastus` 지역에 `batchai.horovod`라는 리소스 그룹을 만듭니다. 리소스 그룹을 사용하여 Batch AI 리소스를 배포합니다.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>작업 영역 만들기

`az batchai workspace create` 명령을 사용하여 Batch AI 작업 영역을 만듭니다. 작업 영역은 다른 Batch AI 리소스 중 최상위 컬렉션입니다. 다음 명령은 리소스 그룹 아래에 `batchaidev`라는 작업 영역을 만듭니다.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>실험 만들기

Batch AI 실험은 쿼리하고 함께 관리하는 하나 이상의 작업을 그룹화합니다. 다음 `az batchai experiment create` 명령은 작업 영역 및 리소스 그룹 아래에 `cifar`라는 실험을 만듭니다.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>GPU 클러스터 설정

다음으로, GPU 클러스터를 설정하여 실험을 실행합니다. Batch AI는 특정 요구에 맞게 클러스터를 사용자 지정하기 위한 다양한 옵션을 제공합니다.

다음 `az batchai cluster create` 명령은 작업 영역 및 리소스 그룹 아래에 `nc6cluster`라는 4노드 클러스터를 만듭니다. 기본적으로 클러스터의 VM은 컨테이너 기반 응용 프로그램을 호스트하도록 디자인된 Ubuntu Server 이미지를 실행합니다. 이 예제의 클러스터 노드는 NVIDIA Tesla K80 GPU 1개를 포함하는 `Standard_NC6` 크기를 사용합니다.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

`az batchai cluster show` 명령을 실행하여 클러스터 상태를 확인합니다. 일반적으로 클러스터를 완전히 프로비전하는 데 몇 분 정도 걸립니다.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

클러스터 생성 초기 단계에 클러스터는 `resizing` 상태입니다. 클러스터 상태가 변경되는 동안 다음 단계를 계속합니다. 클러스터는 상태가 `steady`이고 노드가 `idle`일 때 학습 작업을 실행할 준비가 된 것입니다. 예: 

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>저장소 설정

`az storage account create` 명령을 사용하여 학습 스크립트 및 학습 출력을 저장하기 위한 저장소 계정을 만듭니다.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

`az storage share create` 명령을 사용하여 계정에 `myshare`라는 Azure 파일 공유를 만듭니다.

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

실제로 이 동일한 저장소를 여러 작업 및 실험에서 사용할 수 있습니다. 구성된 상태를 유지하기 위해 파일 공유 내에 이러한 특정 실험과 관련된 파일을 저장할 디렉터리를 만듭니다. 다음 `az storage directory create` 명령은 `cifar`라는 디렉터리를 만듭니다.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

다음 단계는 실제 학습 스크립트를 준비하는 것입니다. 그런 다음, 이 스크립트를 새로 만든 디렉터리에 업로드합니다.

## <a name="create-the-training-script"></a>학습 스크립트 만들기

이 실험에서는 Horovod를 사용하여 개체 감지 모델을 병렬로 실행하기 위해 몇 가지 변경 내용으로 업데이트되는 Python 스크립트를 실행합니다. [원래 모델](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py)은 TensorFlow 백 엔드에서 Keras를 사용합니다. 

셸의 작업 디렉터리에서 즐겨 사용하는 텍스트 편집기를 사용하여 다음 내용의 `cifar_cnn_distributed.py` 파일을 만듭니다. 원래 소스 코드 변경 내용은 `HOROVOD` 접두사를 사용하여 주석 처리합니다.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

이 예제와 같이, Horovod 프레임워크를 사용하여 분산 교육을 사용하도록 설정하기 위해 모델을 약간만 업데이트하면 됩니다. 

이 스크립트는 데모를 위해 비교적 작은 모델 및 데이터 세트를 사용하므로 이 분산 모델이 반드시 획기적인 성능 향상을 보이지는 않을 것입니다. 분산 학습의 진정한 성능을 보려면 훨씬 더 큰 모델 및 데이터 세트를 사용해야 합니다.

## <a name="upload-the-training-script"></a>학습 스크립트 업로드

스크립트가 준비되면 다음 단계로, 이전에 만든 파일 공유 디렉터리에 업로드합니다. 다음 `az storage file upload` 명령은 로컬 작업 디렉터리에서 적절한 위치로 스크립트를 업로드합니다.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>학습 작업 제출

이전 단계를 완료한 후 학습 작업을 만듭니다. Batch AI에서 `job.json` 파일을 사용하여 작업 실행 방법에 대한 매개 변수를 정의합니다. 즐겨 사용하는 텍스트 편집기를 사용하여 다음 내용으로 `job.json`이라는 작업 구성 파일을 만듭니다.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

속성에 대한 설명은 다음과 같습니다.

| 자산 | 설명 |
| --------- | --------- |
| `nodeCount` | 작업 전용 노드의 수입니다. 여기서, 작업은 `4` 노드에서 병렬로 실행됩니다. |
| `horovodSettings` | `pythonScriptFilePath` 필드는 이전에 만든 `cifar` 디렉터리의 Horovod 스크립트 경로를 정의합니다. `commandLineArgs` 필드는 스크립트를 실행하기 위한 명령줄 인수입니다. 이 실험에서는 모델을 저장할 디렉터리만 필수 인수입니다. `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare`는 파일 공유가 탑재된 위치의 경로입니다. | 
| `stdOutErrPathPrefix` | 작업 출력 및 로그를 저장할 경로입니다. 이 예제에서는 동일한 `cifar` 디렉터리가 됩니다. |
| `jobPreparation` | 작업 실행 환경을 준비하기 위한 특별 지침입니다. 이 스크립트에서는 지시된 Horovod 및 MPI 패키지를 설치해야 합니다. |
| `containerSettings` | 작업이 실행되는 컨테이너에 대한 설정입니다. 이 작업에서는 `tensorflow`로 빌드한 Docker 컨테이너를 사용합니다.

해당 구성을 사용하여 `az batchai job create` 명령으로 작업을 만듭니다. 다음 명령은 이 시점까지 설정한 모든 리소스를 사용하여 `cifar_distributed`라는 새 작업을 큐에 대기합니다. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

노드가 현재 사용 중인 경우 작업 실행이 시작되기까지 잠시 시간이 걸릴 수 있습니다. `az batchai job show` 명령을 사용하여 작업의 실행 상태를 확인합니다.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>분산 학습 시각화

작업이 실행되기 시작하면 `az batchai cluster show` 명령을 다시 사용하여 클러스터 노드의 상태를 쿼리합니다. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

출력은 다음과 비슷하며 4개의 노드를 모두 실행 중 상태로 표시합니다. 이 결과는 4개 노드가 현재 분산 학습에서 사용되고 있음을 나타냅니다.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>작업 모니터링

### <a name="list-output-files"></a>출력 파일 나열

작업이 실행되는 동안 `az batchai job file list` 명령을 사용하여 작업이 생성하는 출력 파일을 나열합니다.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

이 특정 실험에서 출력은 다음과 비슷해야 합니다. `stderr.txt`는 주 실행에서 발생하는 모든 오류를 출력하지만 작업의 전반적인 출력은 `stdout.txt`에 로깅됩니다. 기타 파일은 각 개별 노드에 해당하는 출력, 오류 및 작업 준비 로그입니다.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>출력 파일 스트리밍

`az batchai job file stream` 명령을 사용하여 파일의 내용을 스트리밍합니다. 다음 예제에서는 주 출력 로그를 스트리밍합니다.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

작업이 실행되는 동안 이 명령은 작업의 표준 출력을 스트리밍하여 다음과 유사한 출력을 나타냅니다.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

이 스크립트는 25개가 넘는 Epoch를 학습하거나 학습 데이터 세트를 통과합니다. 이 프로세스는 약 60분이 걸립니다. 

## <a name="retrieve-the-results"></a>결과 검색

스크립트가 완료되고 아무 문제가 없으면 유효성 검사 정확도가 약 70-75%여야 하며 학습된 모델이 `cifar/saved_models/keras_cifar10_trained_model.h5`의 파일 공유에 저장됩니다. 

모델 학습은 일반적으로 좀 더 큰 워크플로의 일부입니다. 예를 들어 학습된 모델을 다른 응용 프로그램에서 공개할 수 있습니다. 학습된 모델을 로컬로 다운로드하려면 `az storage file download` 명령을 사용합니다. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>리소스 정리

작업 실행이 완료된 경우, 계산 비용을 절약하는 모범 사례는 유휴 시간 동안 비용이 청구되지 않도록 모든 클러스터 규모를 `0 nodes`로 축소하는 것입니다. 다음 `az batchai cluster resize` 명령을 사용합니다. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

나중에 작업을 실행하려면 클러스터 크기를 하나 이상의 노드로 조정합니다. 

나중에 작업 영역 및 저장소 계정을 사용하지 않으려는 경우 `az group delete` 명령을 사용하여 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 해당 그룹에 있는 리소스도 모두 삭제됩니다.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Batch AI 작업 영역, 실험 및 클러스터 설정
> * 입/출력용 Azure 파일 공유 설정
> * Horovod를 사용하여 모델 병렬 처리
> * 학습 작업 제출
> * 작업 모니터링
> * 학습 결과 검색

다양한 프레임워크에서 Batch AI를 사용하는 예제는 GitHub의 레시피를 참조하세요.

> [!div class="nextstepaction"]
> [Batch AI 레시피](https://github.com/Azure/BatchAI/tree/master/recipes)
