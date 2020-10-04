---
title: Visual Studio Code에서 계산 인스턴스에 연결 (미리 보기)
titleSuffix: Azure Machine Learning
description: 에서 Azure Machine Learning 계산 인스턴스에 연결 하는 방법에 대해 알아봅니다 Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 09/03/2020
ms.openlocfilehash: 6ac116b315d4a11b51b37c5b51edf35aa0676713
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708376"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Visual Studio Code에서 Azure Machine Learning 계산 인스턴스에 연결 (미리 보기)

이 문서에서는 Visual Studio Code를 사용 하 여 Azure Machine Learning 계산 인스턴스에 연결 하는 방법을 알아봅니다.

[Azure Machine Learning 계산 인스턴스](concept-compute-instance.md) 는 데이터 과학자 완전히 관리 되는 클라우드 기반 워크스테이션 이며 IT 관리자에 게 관리 및 엔터프라이즈 준비 기능을 제공 합니다.

Visual Studio Code에서 계산 인스턴스에 연결 하는 방법에는 두 가지가 있습니다.

* 원격 Jupyter Notebook 서버. 이 옵션을 사용 하 여 계산 인스턴스를 원격 Jupyter Notebook 서버로 설정할 수 있습니다.
* [원격 개발을 Visual Studio Code](https://code.visualstudio.com/docs/remote/remote-overview)합니다. Visual Studio Code 원격 개발을 사용 하면 컨테이너, 원격 컴퓨터 또는 WSL (Linux 용 Windows 하위 시스템)을 완전 한 기능을 갖춘 개발 환경으로 사용할 수 있습니다.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>계산 인스턴스를 원격 노트북 서버로 구성

계산 인스턴스를 원격 Jupyter Notebook 서버로 구성 하려면 몇 가지 필수 구성 요소가 필요 합니다.

* Visual Studio Code 확장을 Azure Machine Learning 합니다. 자세한 내용은 [Azure Machine Learning Visual Studio Code 확장 설정 가이드](tutorial-setup-vscode-extension.md)를 참조 하세요.
* Azure Machine Learning 작업 영역입니다. 아직 없는 경우 [Azure Machine Learning Visual Studio Code 확장을 사용 하 여 새 작업 영역을 만듭니다](how-to-manage-resources-vscode.md#create-a-workspace) .

계산 인스턴스에 연결 하려면 다음을 수행 합니다.

1. Visual Studio Code에서 Jupyter Notebook를 엽니다.
1. 통합 된 노트북 환경이 로드 되 면 **Jupyter 서버**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![원격 Jupyter 노트북 서버 드롭다운 Azure Machine Learning 시작](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    또는 명령 팔레트를 사용할 수도 있습니다.

    1. 메뉴 모음에서 **보기 > 명령 팔레트**를 차례로 선택하여 명령 팔레트를 엽니다.
    1. 텍스트 상자에을 입력 `Azure ML: Connect to Compute instance Jupyter server` 합니다.

1. `Azure ML Compute Instances`Jupyter 서버 옵션 목록에서 선택 합니다.
1. 구독 목록에서 구독을 선택 합니다. 이전에 기본 Azure Machine Learning 작업 영역을 구성한 경우이 단계를 건너뜁니다.
1. 작업 영역을 선택합니다.
1. 목록에서 계산 인스턴스를 선택 합니다. 없는 경우 **새 Azure ML 컴퓨팅 인스턴스 만들기** 를 선택 하 고 표시 되는 메시지에 따라 하나를 만듭니다.
1. 변경 내용을 적용 하려면 Visual Studio Code을 다시 로드 해야 합니다.
1. Jupyter Notebook를 열고 셀을 실행 합니다.

> [!IMPORTANT]
> 연결을 설정 하려면 셀을 실행 **해야** 합니다.

이 시점에서 Jupyter 노트북의 셀을 계속 실행할 수 있습니다.

> [!TIP]
> Jupyter 코드 셀을 포함 하는 Python 스크립트 파일 (. py)로 작업할 수도 있습니다. 자세한 내용은 [Visual Studio Code Python 대화형 설명서](https://code.visualstudio.com/docs/python/jupyter-support-py)를 참조 하세요.

## <a name="configure-compute-instance-remote-development"></a>계산 인스턴스 원격 개발 구성

모든 기능을 갖춘 원격 개발 환경을 구현 하려면 몇 가지 필수 구성 요소가 필요 합니다.

* [원격 SSH 확장을 Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)합니다.
* SSH 사용 계산 인스턴스입니다. 자세한 내용은 [compute 인스턴스 만들기 가이드를 참조](how-to-create-manage-compute-instance.md)하세요.

> [!NOTE]
> Windows 플랫폼에서 아직 없는 경우 [OpenSSH 호환 SSH 클라이언트를 설치](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) 해야 합니다. Ssh 명령이 경로에 있어야 하므로 PuTTY는 Windows에서 지원 되지 않습니다.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>계산 인스턴스에 대 한 IP 및 SSH 포트 가져오기

1. Azure Machine Learning studio ()로 이동 https://ml.azure.com/ 합니다.
2. [작업 영역](concept-workspace.md)을 선택 합니다.
1. **계산 인스턴스** 탭을 클릭 합니다.
1. **응용 프로그램 URI** 열에서 원격 계산으로 사용 하려는 계산 인스턴스의 **SSH** 링크를 클릭 합니다. 
1. 대화 상자에서 IP 주소와 SSH 포트를 적어둡니다. 
1. 로컬 컴퓨터의 ~/.ssh/디렉터리에 개인 키를 저장 합니다. 예를 들어 새 파일에 대 한 편집기를 열고 다음 키를 붙여넣습니다. 

   **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   개인 키는 다음과 유사 하 게 표시 됩니다.

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. 파일에 대 한 사용 권한을 변경 하 여 파일을 읽을 수만 있는지 확인 합니다.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>인스턴스를 호스트로 추가

`~/.ssh/config`편집기에서 파일 (Linux) 또는 `C:\Users<username>.ssh\config` (Windows)를 열고 아래 내용과 비슷한 새 항목을 추가 합니다.

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

필드에 대 한 자세한 내용은 다음과 같습니다.

|필드|설명|
|----|---------|
|호스트|계산 인스턴스에 대해 원하는 약어를 사용 합니다. |
|HostName|계산 인스턴스의 IP 주소입니다. |
|포트|위의 SSH 대화 상자에 표시 되는 포트입니다. |
|사용자|다음이 필요 합니다. `azureuser` |
|IdentityFile|는 개인 키를 저장 한 파일을 가리켜야 합니다. |

이제 위에서 사용한 약어를 사용 하 여 계산 인스턴스로 ssh를 실행할 수 있습니다 `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>인스턴스에 VS Code 연결

1. Visual Studio Code 활동 표시줄에서 원격-SSH 아이콘을 클릭 하 여 SSH 구성을 표시 합니다.

1. 방금 만든 SSH 호스트 구성을 마우스 오른쪽 단추로 클릭 합니다.

1. **현재 창에서 호스트에 연결을**선택 합니다. 

여기서는 계산 인스턴스를 완전히 작업 하며, 이제 로컬 Visual Studio Code와 마찬가지로 편집, 디버그, git 사용, 확장 등의 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 원격 Visual Studio Code 설정 했으므로 계산 인스턴스를 Visual Studio Code의 원격 계산으로 사용 하 여 [코드를 대화형으로 디버그할](how-to-debug-visual-studio-code.md)수 있습니다.

[자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 통합 Notebook으로 컴퓨팅 인스턴스를 사용하는 방법을 보여 줍니다.