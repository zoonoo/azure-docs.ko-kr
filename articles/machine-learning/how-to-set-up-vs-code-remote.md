---
title: '대화형 디버그: VS 코드 & ML 계산 인스턴스'
titleSuffix: Azure Machine Learning
description: VS 코드 원격 설정하여 Azure 기계 학습을 사용하여 코드를 대화형으로 디버깅합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169746"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>VS 코드 원격으로 Azure 기계 학습 계산 인스턴스에서 대화형으로 디버그

이 문서에서는 VS 코드에서 **코드를 대화형으로 디버깅할** 수 있도록 Azure 기계 학습 계산 인스턴스에서 Visual Studio 코드 원격을 설정하는 방법을 알아봅니다. 

+ [Azure 기계 학습 컴퓨팅 인스턴스는](concept-compute-instance.md) 데이터 과학자를 위한 완전히 관리되는 클라우드 기반 워크스테이션이며 IT 관리자를 위한 관리 및 엔터프라이즈 준비 기능을 제공합니다. 


+ [비주얼 스튜디오 코드 원격](https://code.visualstudio.com/docs/remote/remote-overview) 개발을 통해 컨테이너, 원격 컴퓨터 또는 WSL용 Windows 하위 시스템을 완전한 기능을 갖춘 개발 환경으로 사용할 수 있습니다. 

## <a name="prerequisite"></a>필수 요소  

Windows 플랫폼에서 [OpenSSH 호환 SSH 클라이언트가](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) 아직 없는 경우 설치해야 합니다. 

> [!Note]
> Ssh 명령이 경로에 있어야 하므로 PuTTY는 Windows에서 지원되지 않습니다. 

## <a name="get-ip-and-ssh-port"></a>IP 및 SSH 포트 받기 

1. 에서 Azure 기계 학습 https://ml.azure.com/스튜디오로 이동합니다.

2. 작업 [영역을](concept-workspace.md)선택합니다.
1. 인스턴스 **계산 탭을 클릭합니다.**
1. 응용 **프로그램 URI** 열에서 원격 계산으로 사용할 계산 인스턴스의 **SSH** 링크를 클릭합니다. 
1. 대화 상자에서 IP 주소와 SSH 포트를 기록해 둡니다. 
1. 로컬 컴퓨터에 ~/.ssh/ 디렉토리에 개인 키를 저장; 예를 들어 새 파일에 대한 편집기열기를 열고 다음 에 키를 붙여넣습니다. 

   **리눅스**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **윈도우**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   개인 키는 다음과 같이 보입니다.
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. 파일의 사용 권한을 변경하여 파일만 읽을 수 있는지 확인합니다.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>인스턴스를 호스트로 추가 

편집기에서 `~/.ssh/config` 파일(Linux) 또는 `C:\Users<username>.ssh\config` (Windows)를 열고 다음과 유사한 새 항목을 추가합니다.

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

여기에 필드에 대한 몇 가지 세부 사항 : 

|필드|설명|
|----|---------|
|호스트|계산 인스턴스에 원하는 약어 를 사용 |
|HostName|계산 인스턴스의 IP 주소입니다. |
|포트|위의 SSH 대화 상자에 표시된 포트입니다. |
|사용자|이 경우 `azureuser` |
|아이덴티티 파일|개인 키를 저장한 파일을 가리가야 합니다. |

이제 위에서 사용한 약어를 사용하여 계산 인스턴스를 ssh 할 `ssh azmlci1`수 있어야합니다. 

## <a name="connect-vs-code-to-the-instance"></a>VS 코드를 인스턴스에 연결 

1. [비주얼 스튜디오 코드를 설치합니다.](https://code.visualstudio.com/)

1. [원격 SSH 확장을 설치합니다.](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) 

1. 왼쪽의 원격 SSH 아이콘을 클릭하여 SSH 구성을 표시합니다.

1. 방금 만든 SSH 호스트 구성을 마우스 오른쪽 단추로 클릭합니다.

1. **현재 창에서 호스트에 연결 선택합니다.** 

여기에서 는 완전히 계산 인스턴스에 대해 작업하고 있으며 이제 로컬 Visual Studio 코드와 마찬가지로 편집, 디버깅, git 사용, 확장 프로그램 사용 등을 수행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 Visual Studio Code Remote를 설정했기 때문에 Visual Studio Code에서 원격 계산으로 계산 인스턴스를 사용하여 대화형으로 코드를 디버깅할 수 있습니다. 

[자습서: 첫 번째 ML 모델을 학습하면](tutorial-1st-experiment-sdk-train.md) 통합 된 노트북을 사용하여 계산 인스턴스를 사용하는 방법을 보여 줍니다.