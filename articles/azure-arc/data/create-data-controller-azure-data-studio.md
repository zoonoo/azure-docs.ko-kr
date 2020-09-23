---
title: Azure Data Studio에서 데이터 컨트롤러 만들기
description: Azure Data Studio에서 데이터 컨트롤러 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71678977f899b910a97dbb552233f36ca5a51f26
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940000"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Azure Data Studio에서 데이터 컨트롤러 만들기

배포 마법사 및 노트북을 통해 Azure Data Studio를 사용 하 여 데이터 컨트롤러를 만들 수 있습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Kubernetes 클러스터에 액세스 하 고 배포 하려는 Kubernetes 클러스터를 가리키도록 kubeconfig 파일을 구성 해야 합니다.
- **Azure Arc** 및 **azure Data CLI**라는 Azure Data Studio 확장 **Azure Data Studio** 포함 하 [는 클라이언트 도구를 설치](install-client-tools.md) 해야 합니다.
- Azure Data Studio에서 Azure에 로그인 해야 합니다.  이렇게 하려면 CTRL/Command + SHIFT + P를 입력 하 여 명령 텍스트 창을 열고 **Azure**를 입력 합니다.  **Azure: 로그인**을 선택 합니다.   패널에서 오른쪽 위에 있는 + 아이콘을 클릭 하 여 Azure 계정을 추가 합니다.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>배포 마법사를 사용 하 여 Azure Arc 데이터 컨트롤러 만들기

배포 마법사를 사용 하 여 Azure Arc 데이터 컨트롤러를 만들려면 다음 단계를 따르세요.

1. Azure Data Studio에서 왼쪽 탐색 영역에 있는 연결 탭을 클릭 합니다.
2. 연결 패널의 맨 위에 있는 ... 단추를 클릭 하 고 **새 배포** **...를 선택** 합니다.
3. 새 배포 마법사에서 **Azure Arc Data Controller**를 선택 하 고, 라이선스 승인 확인란을 선택 하 고 맨 아래에 있는 **선택** 단추를 클릭 합니다.
4. 기본 kubeconfig 파일을 사용 하거나 다른 파일을 선택 합니다.  **다음**을 클릭합니다.
5. Kubernetes 클러스터 컨텍스트를 선택 합니다. **다음**을 클릭합니다.
6. 대상 Kubernetes 클러스터에 따라 배포 구성 프로필 파일을 선택 합니다. **다음을 클릭**합니다.
8. 원하는 구독 및 리소스 그룹을 선택 합니다.
9. 데이터 컨트롤러의 이름과 데이터 컨트롤러가 생성 될 네임 스페이스의 이름을 입력 합니다.  

> [!NOTE]
> 네임 스페이스가 이미 있는 경우 네임 스페이스에 다른 Kubernetes 개체 (pod 등)가 아직 없는 경우 사용 됩니다.  네임 스페이스가 없는 경우에는 네임 스페이스를 만들려고 시도 합니다.  Kubernetes 클러스터에 네임 스페이스를 만들려면 Kubernetes cluster administrator 권한이 필요 합니다.  Kubernetes 클러스터 관리자 권한이 없는 경우 Kubernetes 클러스터 관리자에 게이 마법사를 완료 하기 전에 Kubernetes 관리자가 수행 해야 하는 [Kubernetes를 사용 하 여 데이터 컨트롤러 만들기](./create-data-controller-using-k8s-native-tools.md) 문서에서 처음 몇 단계를 수행 하도록 요청 합니다.

> [!NOTE]
> 참고: 데이터 컨트롤러와 네임 스페이스 이름은 Kubernetes 클러스터에 사용자 지정 리소스를 만드는 데 사용 되므로 [Kubernetes 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)을 준수 해야 합니다.

10. Azure 위치를 선택합니다.
   
> [!NOTE]
> 여기에서 선택한 Azure 위치는 Azure에서 데이터 컨트롤러 및 데이터 컨트롤러에 대 한 *메타 데이터가* 저장 되는 위치입니다.  데이터 컨트롤러와 데이터베이스 인스턴스는 Kubernetes 클러스터에서 실제로 어디에 나 있을 수 있습니다.

11.  사용자 이름 및 암호를 입력 하 고 데이터 컨트롤러 관리자 사용자 계정의 암호를 확인 합니다.

> [!NOTE]
> 암호의 길이는 8자 이상이어야 합니다.

1.  **다음**을 클릭합니다.
2.  검토 하 고 **노트북에 대 한 스크립트를**클릭 합니다.
3.  **생성 된 노트북을 검토**합니다.  저장소 클래스 이름 또는 서비스 유형과 같은 필요한 변경 작업을 수행 합니다.
4.  노트북 위쪽에서 **모두 실행** 을 클릭 합니다.

## <a name="monitoring-the-creation-status"></a>만들기 상태 모니터링

컨트롤러를 만드는 과정을 완료 하는 데 몇 분 정도 걸립니다. 다음 명령을 사용 하 여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령은 ' arc ' 라는 이름으로 데이터 컨트롤러 및 Kubernetes 네임 스페이스를 만들었다고 가정 합니다.  다른 네임 스페이스/데이터 컨트롤러 이름을 사용한 경우 ' arc '를 사용자의 이름으로 바꿀 수 있습니다.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행 하 여 특정 pod의 생성 상태를 확인할 수도 있습니다.  이 기능은 문제를 해결 하는 데 특히 유용 합니다.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>생성 문제 해결

만든 문제가 발생 하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조 하세요.
