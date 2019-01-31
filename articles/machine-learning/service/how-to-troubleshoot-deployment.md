---
title: 배포 문제 해결 가이드
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 사용하여 AKS 및 ACI에서 일반적인 Docker 배포 오류를 해결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 83ee548befdc7ef0a4e7ed2d4b4e61b42a217f12
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247071"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Azure Machine Learning AKS 및 ACI 서비스 배포 문제 해결

이 문서에서는 Azure Machine Learning 서비스를 사용하여 ACI(Azure Container Instances) 및 AKS(Azure Kubernetes Service)에서 일반적인 Docker 배포 오류를 해결하는 방법을 알아봅니다.

Azure Machine Learning 서비스에서 모델을 배포할 때 시스템에서 많은 작업을 수행합니다. 이 과정은 매우 복잡한 일련의 이벤트이며 종종 문제가 발생합니다. 배포 작업은 다음과 같습니다.

1. 모델을 작업 영역 모델 레지스트리에 등록합니다.

2. 다음을 포함하여 Docker 이미지를 빌드합니다.
    1. 레지스트리에서 등록 모델을 다운로드합니다. 
    2. 환경 yaml 파일에 지정된 종속성 기반의 Python 환경을 사용하여 dockerfile을 만듭니다.
    3. dockerfile에 제공하는 모델 파일 및 채점 스크립트를 추가합니다.
    4. dockerfile을 사용하여 새 Docker 이미지를 빌드합니다.
    5. 작업 영역과 연결된 Azure Container Registry에 Docker 이미지를 등록합니다.

3. ACI(Azure Container Instance) 서비스 또는 AKS(Azure Kubernetes Service)에 Docker 이미지를 배포합니다.

4. ACI 또는 AKS에서 새 컨테이너(또는 여러 개의 새 컨테이너)를 시작합니다. 

이 프로세스에 대한 자세한 정보는 [모델 관리](concept-model-management-and-deployment.md) 소개를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

문제가 발생할 경우 가장 먼저 할 일은 배포 작업을 개별 단계로 분리하여(이전 설명 참조) 문제를 격리하는 것입니다. 

이 방법은 `Webservice.deploy` API 또는 `Webservice.deploy_from_model` API를 사용 중인 경우에 특히 유용합니다. 왜냐하면 이러한 함수는 앞에서 언급한 단계를 단일 작업으로 그룹화하기 때문입니다. 일반적으로 이러한 API는 상당히 편리하지만, 문제를 해결할 때 아래의 API 호출로 대체하여 단계를 분리하는 데 도움이 됩니다.

1. 모델을 등록합니다. 다음은 샘플 코드입니다.

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 이미지를 빌드합니다. 다음은 샘플 코드입니다.

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. 이미지를 서비스로 배포합니다. 다음은 샘플 코드입니다.

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

배포 프로세스를 개별 작업으로 분리한 후에는 가장 일반적인 오류 중 일부를 볼 수 있습니다.

## <a name="image-building-fails"></a>이미지 빌드 실패
시스템에서 Docker 이미지를 빌드할 수 없는 경우 단서가 될 수 있는 오류 메시지와 함께 `image.wait_for_creation()` 호출이 실패합니다. 또한 이미지 빌드 로그에서 오류에 대한 자세한 내용을 찾을 수 있습니다. 아래는 이미지 빌드 로그 uri를 검색하는 방법을 보여주는 샘플 코드입니다.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```
이미지 로그 uri는 Azure Blob Storage에 저장된 로그 파일을 가리키는 SAS URL입니다. 간단하게 uri를 복사하여 브라우저 창에 붙여넣는 방법으로 로그 파일을 다운로드하여 볼 수 있습니다.


## <a name="service-launch-fails"></a>서비스 시작 실패
이미지가 성공적으로 빌드되면 시스템이 배포 구성에 따라 ACI 또는 AKS에서 컨테이너를 시작하려고 시도합니다. 일반적으로 좀 더 단순한 형태의 단일 컨테이너 배포인 ACI 배포를 먼저 시도하는 것이 좋습니다. 이 방법으로 모든 AKS 관련 문제를 제외할 수 있습니다.

컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다. 아래는 문제를 해결하는 팁입니다.

### <a name="inspect-the-docker-log"></a>Docker 로그 검사
서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Docker 이미지를 로컬로 디버그
Docker 로그에 오류에 대한 정보가 충분히 제공되지 않는 경우가 가끔 있습니다. 한 단계 더 나아가서 빌드된 Docker 이미지를 끌어오고, 로컬 컨테이너를 시작하고, 라이브 컨테이너 내부에서 대화형으로 직접 디버그할 수 있습니다. 로컬 컨테이너를 시작하려면 로컬에서 실행 중인 Docker 엔진이 있어야 하며, [azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 설치되어 있으면 훨씬 쉽습니다.

먼저 이미지 위치를 확인해야 합니다.

```python
# print image location
print(image.image_location)
```

이미지 위치는 `<acr-name>.azurecr.io/<image-name>:<version-number>` 형식입니다(예: `myworkpaceacr.azurecr.io/myimage:3`). 

이제 명령줄 창으로 이동합니다. azure-cli가 설치된 경우 다음 명령을 입력하여 이미지가 저장되는 작업 영역과 연결된 ACR(Azure Container Registry)에 로그인할 수 있습니다. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
azure-cli가 설치되지 않은 경우 `docker login` 명령을 사용하여 ACR에 로그인할 수 있습니다. 하지만 먼저 Azure Portal에서 ACR의 사용자 이름 및 암호를 검색해야 합니다.

ACR에 로그인했으면 Docker 이미지를 끌어오고 컨테이너를 로컬로 시작한 다음, `docker run` 명령을 사용하여 디버깅을 위한 bash 세션을 시작할 수 있습니다.

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

컨테이너를 실행 중인 Bash 세션을 시작하면 `/var/azureml-app` 폴더에서 채점 스크립트를 찾을 수 있습니다. 그런 다음, Python 세션을 시작하여 채점 스크립트를 디버그할 수 있습니다. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
스크립트를 수정할 텍스트 편집기가 필요한 경우 vim, nano, Emacs 또는 선호하는 다른 편집기를 설치하면 됩니다.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

로컬로 웹 서비스를 시작하고 HTTP 트래픽을 해당 서비스로 보낼 수도 있습니다. Docker 컨테이너의 Flask 서버는 포트 5001에서 실행됩니다. 호스트 머신에서 사용할 수 있는 다른 포트에 매핑할 수 있습니다.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>함수 실패: get_model_path()
종종 채점 스크립트의 `init()` 함수에서, 컨테이너의 모델 파일 또는 모델 파일의 폴더를 찾기 위해 `Model.get_model_path()` 함수가 호출됩니다. 모델 파일 또는 폴더를 찾을 수 없는 경우 이로 인해 문제가 발생합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

이렇게 하면 채점 스크립트가 모델 파일 또는 폴더를 찾을 것으로 예상되는 컨테이너의 로컬 경로(`/var/azureml-app`의 상대 경로)가 인쇄됩니다. 그러면 예상 위치에 파일 또는 폴더가 실제로 있는지 확인할 수 있습니다.


## <a name="function-fails-runinputdata"></a>함수 실패: run(input_data)
서비스가 성공적으로 배포되었지만 채점 엔드포인트에 데이터를 게시할 때 크래시가 발생하는 경우 오류를 catch하는 명령문을 `run(input_data)` 함수에 추가하면 구체적인 오류 메시지가 반환됩니다. 예: 

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```
**참고**: `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 프로덕션 환경에서 이 방법을 사용하는 것은 보안에 좋지 않습니다.


## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요. 
* [배포 방법 및 위치](how-to-deploy-and-where.md)

* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
