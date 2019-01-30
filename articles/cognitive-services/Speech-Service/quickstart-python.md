---
title: '빠른 시작: 음성 인식, Python - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 가이드에 따라 Python용 Speech SDK를 사용하여 음성을 텍스트로 변환하는 콘솔 애플리케이션을 만듭니다. 작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 40869457ce933368e17a2054dfca50fc4505fa22
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381583"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>빠른 시작: Python용 Speech SDK를 사용하여 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Python용 Speech SDK를 통해 Speech Service를 사용하는 방법을 보여줍니다. 마이크 입력에서 음성을 인식하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 필수 구성 요소 목록은 다음과 같습니다.

* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).
* [Python 3.5(64비트)](https://www.python.org/downloads/) 이상
* Python Speech SDK 패키지는 Windows(x64), Mac(macOS X 버전 10.12 이상) 및 Linux(x64의 Ubuntu 16.04 또는 18.04)에서 사용할 수 있습니다.
* Ubuntu에서 필요한 패키지의 설치를 위해 다음 명령을 실행합니다.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* Windows에서 플랫폼에 대한 [Visual Studio 2017용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)가 필요합니다.

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK Python 패키지는 명령줄에서 이 명령을 사용하여 [PyPI](https://pypi.org/)에서 설치할 수 있습니다.

```sh
pip install azure-cognitiveservices-speech
```

Cognitive Services 음성 SDK의 현재 버전은 `1.2.0`입니다.

## <a name="support-and-updates"></a>지원 및 업데이트

Speech SDK Python 패키지에 대한 업데이트는 PyPI를 통해 배포되며, [릴리스 정보](./releasenotes.md) 페이지에서 발표됩니다.
새 버전을 사용할 수 있는 경우 `pip install --upgrade azure-cognitiveservices-speech` 명령을 사용하여 업데이트할 수 있습니다.
`azure.cognitiveservices.speech.__version__` 변수를 검사하여 현재 설치된 버전을 확인할 수 있습니다.

문제가 있거나 기능이 누락된 경우 [지원 페이지](./support.md)를 참조하세요.

## <a name="create-a-python-application-using-the-speech-sdk"></a>Speech SDK를 사용하여 Python 애플리케이션 만들기

### <a name="run-the-sample"></a>샘플 실행

이 빠른 시작에서 원본 파일 `quickstart.py`로 [코드](#quickstart-code)를 복사하고 IDE 또는 콘솔에서 실행할 수 있습니다.

```sh
python quickstart.py
```

또는 [Cognitive Services Speech 샘플 리포지토리](https://github.com/Azure-Samples/cognitive-services-speech-sdk/)에서 [Jupyter](https://jupyter.org) Notebook으로 이 빠른 시작 자습서를 다운로드하고 노트북으로 실행할 수 있습니다.

### <a name="sample-code"></a>샘플 코드

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Visual Studio Code를 사용하여 Speech SDK 설치 및 사용

1. 컴퓨터에 Python의 64비트 버전(3.5 이상)을 [다운로드](https://www.python.org/downloads/)하고 설치합니다.
1. Visual Studio Code를 [다운로드](https://code.visualstudio.com/Download)하여 설치합니다.
1. Visual Studio Code를 열고 메뉴에서 **파일** > **기본 설정** > **확장**을 선택하고 "Python"을 검색하여 Python 확장을 설치합니다.
   ![Python 확장 설치](media/sdk/qs-python-vscode-python-extension.png)
1. 예를 들어 Windows 탐색기를 사용하여 프로젝트를 저장할 폴더를 만듭니다.
1. Visual Studio Code에서 **파일** 아이콘을 클릭한 다음, 사용자가 만든 폴더를 엽니다.
   ![폴더 열기](media/sdk/qs-python-vscode-python-open-folder.png)
1. 새 파일 아이콘을 클릭하여 새 Python 원본 파일 `speechsdk.py`를 만듭니다.
   ![파일 만들기](media/sdk/qs-python-vscode-python-newfile.png)
1. 새로 생성된 파일에 [Python 코드](#quickstart-code)를 복사, 붙여넣기 및 저장합니다.
1. Speech Service 구독 정보를 삽입합니다.
1. Python 인터프리터가 이미 선택된 경우 창 아래쪽의 상태 표시줄 왼쪽에 표시됩니다.
   그렇지 않으면 **명령 팔레트**(`Ctrl+Shift+P`)를 열고 **Python: 인터프리터 선택**을 입력하여 사용 가능한 Python 인터프리터의 목록을 가져오고, 적절한 항목을 선택할 수 있습니다.
1. 선택한 Python 인터프리터에 대한 Speech SDK Python 패키지가 아직 설치되지 않은 경우 Visual Studio Code 내에서 손쉽게 수행할 수 있습니다.
   Speech SDK 패키지를 설치하려면 명령 팔레트를 다시 가져오고(`Ctrl+Shift+P`) **터미널: 새 통합 터미널 만들기**를 입력하여 터미널을 엽니다.
   열리는 터미널에서 `python -m pip install azure-cognitiveservices-speech` 명령 또는 시스템에 대한 적절한 명령을 입력합니다.
1. 샘플 코드를 실행하려면 편집기 내의 임의 위치를 마우스 오른쪽 단추로 클릭하고 **터미널에서 Python 파일 실행**을 선택합니다.
   프롬프트가 나타나면 몇 가지 단어를 말하고, 기록된 텍스트가 이후에 표시됩니다.
   ![샘플 실행](media/sdk/qs-python-vscode-python-run.png)

이러한 지침에 따른 문제가 있는 경우 더 광범위한 [Visual Studio Code Python 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 Python 샘플 살펴보기](https://aka.ms/csspeech/samples)
