---
title: '자습서: Python을 사용하여 몰입형 리더 시작'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 몰입형 리더를 시작하는 Python 애플리케이션을 만듭니다.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 5e33108c9fc674abaf980a1272cca31aa21cffff
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990395"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>자습서: Python 샘플 프로젝트를 사용하여 몰입형 리더 시작

[개요](./overview.md)에서는 몰입형 판독기란 무엇이며 어떻게 언어 학습자, 신흥 독자 및 학습 차이가 있는 사람들의 독해력 향상을 위해 입증된 기술을 구현하는지에 대해 알아보았습니다. 이 자습서에서는 몰입형 리더를 시작하는 Python 웹 애플리케이션을 만드는 방법에 대해 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 샘플 프로젝트를 사용하여 Pip, Flask, Jinja 및 virtualenv를 통해 Python 웹앱 만들기
> * 액세스 토큰 획득
> * 샘플 콘텐츠를 사용하여 몰입형 판독기 시작

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* Azure AD(Azure Active Directory) 인증에 대해 구성된 몰입형 리더 리소스입니다. [다음 지침](./azure-active-directory-authentication.md)에 따라 설정하세요. 환경 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.
* [Git](https://git-scm.com/)
* [몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) 및 [pip](https://docs.python.org/3/installing/index.html). Python 3.4부터 pip는 기본적으로 Python 이진 설치 관리자에 포함됩니다.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) 및 [Windows용 virtualenvwrapper-win](https://pypi.org/project/virtualenvwrapper-win/) 또는 [OSX용 virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [requests 모듈](https://pypi.org/project/requests/2.7.0/)
* [Visual Studio Code](https://code.visualstudio.com/)와 같은 IDE

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 인증 토큰 획득

Azure AD 인증 토큰을 검색하는 백 엔드 API를 작성합니다.

이 부분에는 위의 Azure AD 인증 구성 필수 요소 단계의 값이 필요합니다. 해당 세션에서 저장한 텍스트 파일을 다시 참조합니다.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

값을 얻었으면 _.env_라는 새 파일을 만들고, 다음 코드를 이 파일에 붙여넣고, 위의 사용자 지정 속성 값을 입력합니다. 샘플 앱의 _.env._ 파일을 새로 만든 파일로 바꿉니다.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

이 파일에는 공개되어서는 안 되는 비밀이 있으므로 이 파일을 소스 제어에 커밋하지 않아야 합니다.

권한 없는 사용자가 토큰을 획득하여 몰입형 리더 서비스 및 청구에 사용하지 못하도록 **getimmersivereadertoken** API 엔드포인트를 인증 형식(예: [OAuth](https://oauth.net/2/))으로 보호해야 하며, 이 작업은 이 자습서의 범위를 벗어납니다.

## <a name="create-a-python-web-app-on-windows"></a>Windows에서 Python 웹앱 만들기

Windows에서 `flask`를 사용하여 Python 웹앱을 만듭니다.

[Git](https://git-scm.com/)를 설치합니다.

Git이 설치되면 명령 프롬프트를 열고, 몰입형 리더 SDK Git 리포지토리를 컴퓨터의 폴더에 '복제'합니다.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

[Python](https://www.python.org/downloads/) 설치

'PATH에 Python 추가' 확인란을 선택합니다.

![Python Windows 설치 대화 상자 1단계](./media/pythoninstallone.jpg)

확인란을 선택하여 [선택적 기능]을 추가하고, '다음' 단추를 클릭합니다.

![Python Windows 설치 대화 상자 2단계](./media/pythoninstalltwo.jpg)

'사용자 지정 설치'를 선택하고, 설치 경로를 루트 폴더(예: `C:\Python37-32\`)로 설정한 다음, '설치' 단추를 클릭합니다.

![Python Windows 설치 대화 상자 3단계](./media/pythoninstallthree.jpg)

Python 설치가 완료되면 명령 프롬프트를 열고, `cd`를 실행하여 Python Scripts 폴더로 이동합니다.

```cmd
cd C:\Python37-32\Scripts
```

Flask를 설치합니다.

```cmd
pip install flask
```

Jinja2를 설치합니다. 완전한 기능을 갖춘 Python용 템플릿 엔진입니다.

```cmd
pip install jinja2
```

virtualenv를 설치합니다. 격리된 Python 환경을 만드는 도구입니다.

```cmd
pip install pip install virtualenv
```

virtualenvwrapper-win을 설치합니다. virtualenvwrapper의 기본 개념은 virtualenv를 쉽게 사용할 수 있게 하는 것입니다.

```cmd
pip install virtualenvwrapper-win
```

requests 모듈을 설치합니다. requests는 Python으로 작성된 Apache2 공인 HTTP 라이브러리입니다.

```cmd
pip install requests
```

가상 환경을 만듭니다.

```cmd
mkvirtualenv advanced-python
```

`cd`를 실행하여 샘플 프로젝트 루트 폴더로 이동합니다.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

샘플 프로젝트를 환경과 연결합니다. 그러면 새로 만든 가상 환경이 샘플 프로젝트 루트 폴더에 매핑됩니다.

```cmd
setprojectdir .
```

가상 환경을 활성화합니다.

```cmd
activate
```

이제 프로젝트가 활성화되고 명령 프롬프트에 `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>`과 같이 표시됩니다.

환경을 비활성화합니다.

```cmd
deactivate
```

이제 환경이 비활성화되었으므로 `(advanced-python)` 접두사가 사라집니다.

환경을 다시 활성화하려면 샘플 프로젝트 루트 폴더에서 `workon advanced-python`을 실행합니다.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 판독기 시작

환경이 활성화되면 샘플 프로젝트 루트 폴더에서 `flask run`을 입력하여 샘플 프로젝트를 실행합니다.

```cmd
flask run
```

브라우저를 열고, _http://localhost:5000_ 으로 이동합니다.

## <a name="create-a-python-web-app-on-osx"></a>OSX에서 Python 웹앱 만들기

OSX에서 `flask`을 사용하여 Python 웹앱을 만듭니다.

[Git](https://git-scm.com/)를 설치합니다.

Git이 설치되면 터미널을 열고, 몰입형 리더 SDK Git 리포지토리를 컴퓨터의 폴더에 '복제'합니다.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

[Python](https://www.python.org/downloads/) 설치

이제 Python 루트 폴더(예: `Python37-32`)가 Applications 폴더에 있습니다.

Python 설치가 완료되면 터미널을 열고, `cd`를 실행하여 Python Scripts 폴더로 변경합니다.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

pip를 설치합니다.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

그런 다음, 권한 문제를 방지하기 위해 다음을 실행하여 현재 로그인한 사용자의 pip를 설치합니다.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- 메시지가 표시되면 암호를 입력합니다.
- pip 설치 경로를 PATH 변수에 추가합니다.
- 파일 아래쪽으로 이동하여 목록의 마지막 항목으로 추가하려는 경로(예: `PATH=$PATH:/usr/local/bin`)를 입력합니다.
- control-x를 눌러 종료합니다.
- `Y`를 입력하여 수정된 버퍼를 저장합니다.
- 이것으로 끝입니다. 테스트하려면 새 터미널 창에서 `echo $PATH`를 입력합니다.

Flask를 설치합니다.

```bash
pip install flask --user
```

Jinja2를 설치합니다. 완전한 기능을 갖춘 Python용 템플릿 엔진입니다.

```bash
pip install Jinja2 --user
```

virtualenv를 설치합니다. 격리된 Python 환경을 만드는 도구입니다.

```bash
pip install virtualenv --user
```

virtualenvwrapper를 설치 합니다. virtualenvwrapper의 기본 개념은 virtualenv를 쉽게 사용할 수 있게 하는 것입니다.

```bash
pip install virtualenvwrapper --user
```

requests 모듈을 설치합니다. requests는 Python으로 작성된 Apache2 공인 HTTP 라이브러리입니다.

```bash
pip install requests --user
```

가상 환경을 유지하려는 폴더를 선택하고, 다음 명령을 실행합니다.

```bash
mkdir ~/.virtualenvs
```

`cd`를 실행하여 몰입형 리더 SDK Python 샘플 애플리케이션 폴더로 이동합니다.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

가상 환경을 만듭니다.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

샘플 프로젝트를 환경과 연결합니다. 그러면 새로 만든 가상 환경이 샘플 프로젝트 루트 폴더에 매핑됩니다.

```bash
setprojectdir .
```

가상 환경을 활성화합니다.

```bash
activate
```

이제 프로젝트가 활성화되고 명령 프롬프트에 `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>`과 같이 표시됩니다.

환경을 비활성화합니다.

```bash
deactivate
```

이제 환경이 비활성화되었으므로 `(advanced-python)` 접두사가 사라집니다.

환경을 다시 활성화하려면 샘플 프로젝트 루트 폴더에서 `workon advanced-python`을 실행합니다.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 판독기 시작

환경이 활성화되면 샘플 프로젝트 루트 폴더에서 `flask run`을 입력하여 샘플 프로젝트를 실행합니다.

```bash
flask run
```

브라우저를 열고, _http://localhost:5000_ 으로 이동합니다.

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)에서 코드 샘플 보기
