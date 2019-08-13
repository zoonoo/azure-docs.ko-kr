---
title: '자습서: Azure Functions에서 Python 및 TensorFlow를 사용하여 기계 학습 유추 | Microsoft Docs'
description: 이 자습서에서는 Azure Functions에서 TensorFlow 기계 학습 모델을 적용하는 방법을 보여 줍니다.
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: abc7302ee59103a9cbab156b95a41b77eb95d474
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729160"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>자습서: Azure Functions에서 Python 및 TensorFlow를 사용하여 기계 학습 모델 적용

이 문서에서는 Azure Functions를 통해 Python 및 TensorFlow를 기계 학습 모델에 사용하여 해당 콘텐츠에 따라 이미지를 분류하는 방법을 보여 줍니다.

이 자습서에서는 다음에 대해 알아봅니다. 

> [!div class="checklist"]
> * Python에서 Azure Functions를 개발하기 위한 로컬 환경 초기화
> * 사용자 지정 TensorFlow 기계 학습 모델을 함수 앱으로 가져오기
> * 사진에 개 또는 고양이가 포함되어 있는지 예측하기 위한 서버리스 HTTP API 빌드
> * 웹 애플리케이션에서 API 사용

![완성된 프로젝트의 스크린샷](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건 

Python에서 Azure Functions를 만들려면 몇 가지 도구를 설치해야 합니다.

- [Python 3.6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions 핵심 도구](functions-run-local.md#install-the-azure-functions-core-tools)
- [Visual Studio Code](https://code.visualstudio.com/) 같은 코드 편집기

## <a name="clone-the-tutorial-repository"></a>자습서 리포지토리 복제

시작하려면 터미널을 열고, Git을 사용하여 다음 리포지토리를 복제합니다.

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

리포지토리에는 몇 가지 폴더가 포함되어 있습니다.

- *start*:  자습서의 작업 폴더입니다.
- *end*: 최종 결과이며 참조에 대한 전체 구현입니다.
- *resources*: 기계 학습 모델 및 도우미 라이브러리를 포함합니다.
- *frontend*: 함수 앱을 호출하는 웹 사이트입니다.

## <a name="create-and-activate-a-python-virtual-environment"></a>Python 가상 환경 만들기 및 활성화

Azure Functions에는 Python 3.6.x가 필요합니다. 필요한 Python 버전을 사용할 수 있도록 가상 환경을 만듭니다.

현재 작업 디렉터리를 *start* 폴더로 변경합니다. 그런 다음, *.venv*라는 가상 환경을 만들고 활성화합니다. Python 설치에 따라 Python 3.6 가상 환경을 만드는 명령은 다음 지침과 다를 수 있습니다.

#### <a name="linux-and-macos"></a>Linux 및 macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

이제 터미널 프롬프트에는 가상 환경이 성공적으로 활성화되었음을 나타내는 `(.venv)`라는 접두사가 붙습니다. 가상 환경의 `python`이 실제로 Python 3.6.x인지 확인합니다.

```console
python --version
```

> [!NOTE]
> 자습서의 나머지 부분에서는 가상 환경에서 명령을 실행합니다. 터미널에서 가상 환경을 다시 활성화해야 하는 경우 운영 체제에 적합한 활성화 명령을 실행하세요.

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

*start* 폴더에서 Azure Functions Core Tools를 사용하여 Python 함수 앱을 초기화합니다.

```console
func init --worker-runtime python
```

함수 앱에는 하나 이상의 Azure Functions가 포함될 수 있습니다. 편집기에서 *start* 폴더를 열고, 내용을 검사합니다.

- [*local.settings.json*](functions-run-local.md#local-settings-file): 로컬 개발에 사용되는 애플리케이션 설정을 포함합니다.
- [*host.json*](functions-host-json.md): Azure Functions 호스트 및 확장에 대한 설정을 포함합니다.
- [*requirements.txt*](functions-reference-python.md#python-version-and-package-management): 이 애플리케이션에 필요한 Python 패키지를 포함합니다.

## <a name="create-an-http-function"></a>HTTP 함수 만들기

애플리케이션에는 이미지 URL을 입력으로 사용하고 이미지에 개 또는 고양이가 포함되어 있는지 여부에 대한 예측을 반환하는 단일 HTTP API 엔드포인트가 필요합니다.

터미널에서 Azure Functions Core Tools를 사용하여 *classify*라는 새 HTTP 함수를 스캐폴드합니다.

```console
func new --language python --template HttpTrigger --name classify
```

두 개의 파일이 포함된 *classify*라는 새 폴더가 만들어집니다.

- *\_\_init\_\_.py*: main 함수에 대한 파일
- *function.json*:  함수의 트리거 및 해당 입력 및 출력 바인딩을 설명하는 파일

### <a name="run-the-function"></a>함수 실행

Python 가상 환경이 활성화된 터미널에서 함수 앱을 시작합니다.

```console
func start
```

브라우저를 열고 다음 URL로 이동합니다. 이 함수는 *Hello Azure!* 를 실행하고 반환해야 합니다.

```
http://localhost:7071/api/classify?name=Azure
```

`Ctrl-C`를 사용하여 함수 앱을 중지합니다.

## <a name="import-the-tensorflow-model"></a>TensorFlow 모델 가져오기

Azure Custom Vision Service에서 학습시키고 내보낸 미리 작성된 TensorFlow 모델을 사용합니다.

> [!NOTE]
> Custom Vision Service의 체험 계층을 사용하여 직접 빌드하려면 [샘플 프로젝트 리포지토리의 지침](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)을 따를 수 있습니다.

모델은 *<REPOSITORY_ROOT>/resources/model* 폴더의 두 파일인 *model.db* 및 *labels.txt*로 구성됩니다. 이러한 파일을 *classify* 함수의 폴더에 복사합니다.

#### <a name="linux-and-macos"></a>Linux 및 macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

위의 명령에 \*를 포함시킵니다. 이제 *classify*에 *model.pb* 및 *labels.txt*라는 파일이 포함되어 있는지 확인합니다.

## <a name="add-the-helper-functions-and-dependencies"></a>도우미 함수 및 종속성 추가

입력 이미지를 준비하고 TensorFlow를 사용하여 예측하는 일부 도우미 함수는 *resources* 폴더의 *predict.py* 파일에 있습니다. 이 파일을 *classify* 함수의 폴더에 복사합니다.

#### <a name="linux-and-macos"></a>Linux 및 macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

이제 *classify*에 *predict.py*.라는 파일이 포함되어 있는지 확인합니다.

### <a name="install-dependencies"></a>종속성 설치

도우미 라이브러리에는 설치해야 하는 몇 가지 종속성이 있습니다. 편집기에서 *start/requirements.txt*를 열고, 다음 종속성을 파일에 추가합니다.

```txt
tensorflow
Pillow
requests
```

파일을 저장합니다.

가상 환경이 활성화된 터미널에서 *start* 폴더에 있는 다음 명령을 실행하여 종속성을 설치합니다. 일부 설치 단계는 완료하는 데 몇 분 정도 걸릴 수 있습니다.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>글로벌 변수에서 모델 캐싱

편집기에서 *predict.py*를 열고, 파일의 위쪽 근처에 있는 `_initialize` 함수를 확인합니다. TensorFlow 모델은 함수를 처음 실행하고 글로벌 변수에 저장할 때 디스크에서 로드됩니다. `_initialize` 함수의 후속 실행에서는 디스크에서 로딩을 건너뜁니다. 이 기법을 사용하여 모델을 메모리에 캐시하면 이후의 예측이 빨라집니다.

글로벌 변수에 대한 자세한 내용은 [Azure Functions Python 개발자 가이드](functions-reference-python.md#global-variables)를 참조하세요.

## <a name="update-function-to-run-predictions"></a>예측을 실행하도록 함수 업데이트

편집기에서 *classify/\_\_init\_\_.py*를 엽니다. 이전에 동일한 폴더에 추가한 *predict* 라이브러리를 가져옵니다. 다음 `import` 문을 이미 파일에 있는 다른 가져오기 아래에 추가합니다.

```python
import json
from .predict import predict_image_from_url
```

함수 템플릿 코드를 다음으로 바꿉니다.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

변경 내용을 저장합니다.

이 함수는 `img`라는 쿼리 문자열 매개 변수의 이미지 URL을 받습니다. 이미지를 다운로드하고 TensorFlow 모델을 사용하여 예측을 반환하는 도우미 라이브러리에서 `predict_image_from_url`을 호출합니다. 그런 다음, 함수에서 결과가 포함된 HTTP 응답을 반환합니다.

HTTP 엔드포인트는 다른 도메인에서 호스팅되는 웹 페이지에서 호출하므로 HTTP 응답에는 브라우저의 CORS(원본 간 리소스 공유) 요구 사항을 충족하는 `Access-Control-Allow-Origin` 헤더가 포함됩니다.

> [!NOTE]
> 프로덕션 애플리케이션에서 보안을 강화하기 위해 `*`를 웹 페이지의 특정 원본으로 변경합니다.

### <a name="run-the-function-app"></a>함수 앱 실행

Python 가상 환경이 아직 활성화되어 있는지 확인하고, 다음 명령을 사용하여 함수 앱을 시작합니다.

```console
func start
```

브라우저에서 고양이 사진의 URL을 사용하여 함수를 호출하는 이 URL을 엽니다. 올바른 예측 결과가 반환되는지 확인합니다.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

함수 앱을 계속 실행합니다.

### <a name="run-the-web-app"></a>웹앱 실행

*frontend* 폴더에는 함수 앱에서 HTTP API를 사용하는 간단한 웹앱이 있습니다.

*별도*의 터미널을 열고, *frontend* 폴더로 변경합니다. Python 3.6에서 HTTP 서버를 시작합니다.

#### <a name="linux-and-macos"></a>Linux 및 macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

브라우저에서 터미널에 표시되는 HTTP 서버의 URL로 이동합니다. 웹앱이 표시됩니다. 다음 사진 URL 중 하나를 텍스트 상자에 입력합니다. 공개적으로 액세스할 수 있는 고양이 또는 개 사진의 URL을 사용할 수도 있습니다.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

제출을 클릭하면 함수 앱이 호출되고 결과가 페이지에 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리
이 자습서 전체가 머신에서 로컬로 실행되므로 정리할 Azure 리소스 또는 서비스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Functions에서 HTTP API를 빌드하고 사용자 지정하여 TensorFlow 모델을 통해 예측하는 방법을 알아보았습니다. 또한 웹 애플리케이션에서 API를 호출하는 방법도 알아보았습니다.

Azure Functions에서 제공하는 서버리스 컴퓨팅 모델에서 실행하는 동안 이 자습서의 기법을 사용하여 모든 복잡한 API를 빌드할 수 있습니다.

함수 앱을 Azure에 배포하려면 [Azure Functions Core Tools](./functions-run-local.md#publish) 또는 [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions)를 사용합니다.

> [!div class="nextstepaction"]
> [Azure Functions Python 개발자 가이드](./functions-reference-python.md)
