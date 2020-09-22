---
title: Azure Portal에서 Azure Arc 데이터 컨트롤러 만들기
description: Azure Portal에서 Azure Arc 데이터 컨트롤러 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 297efa83fb1563e3a360f652a6ac1bc2b1679998
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939977"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Azure Portal에서 Azure Arc 데이터 컨트롤러 만들기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>소개

Azure Portal를 사용 하 여 Azure Arc 데이터 컨트롤러를 만들 수 있습니다.

Azure Arc의 많은 생성 환경은 만들거나 관리할 리소스가 Azure 인프라 외부에 있는 경우에도 Azure Portal에서 시작 합니다. 이러한 경우 사용자 환경 패턴은 특히, Azure와 사용자 환경 간에 직접 연결 되지 않는 경우 Azure Portal를 사용 하 여 스크립트를 생성 한 다음 사용자 환경에서 다운로드 하 여 실행 하 여 Azure에 보안 연결을 다시 설정 하는 것입니다. 예를 들어 Azure Arc 사용 서버는 [arc 사용 서버를 만들기](../servers/onboard-portal.md)위해이 패턴을 따릅니다.

현재 미리 보기에서는 Azure Arc 사용 데이터 서비스의 간접 연결 모드만 지원 하기 위해 Azure Portal를 사용 하 여 Kubernetes 클러스터에 대해 다운로드 하 여 Azure Data Studio에서 실행할 수 있는 노트북을 생성할 수 있습니다. 나중에 직접 연결 모드를 사용할 수 있는 경우 Azure Portal에서 직접 데이터 컨트롤러를 프로 비전 할 수 있습니다. [연결 모드](connectivity.md)에 대 한 자세한 내용을 볼 수 있습니다.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Azure Portal를 사용 하 여 Azure Arc 데이터 컨트롤러 만들기

Azure Portal 및 Azure Data Studio를 사용 하 여 Azure Arc 데이터 컨트롤러를 만들려면 아래 단계를 따르세요.

1. 먼저 [Azure Portal marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)에 로그인 합니다.  Marketplace 검색 결과가 ' Azure Arc data controller '를 표시 하도록 필터링 됩니다.
2. 첫 번째 단계에서 검색 조건을 입력 하지 않은 경우 검색 결과에를 입력 하 고 ' Azure Arc data controller '를 클릭 하세요.
3. Marketplace에서 Azure Data Controller 타일을 선택 합니다.
4. **만들기** 단추를 클릭합니다.
5. Azure Arc 데이터 컨트롤러를 만들고 Azure Data Studio 및 kubectl와 같은 누락 된 필수 구성 요소 소프트웨어를 설치 하려면 요구 사항을 검토 하세요.
6. **데이터 컨트롤러 세부 정보** 단추를 클릭 합니다.
7. Azure Portal에서 만든 다른 리소스와 마찬가지로 구독, 리소스 그룹 및 Azure 위치를 선택 합니다. 이 경우 선택 하는 Azure 위치는 리소스에 대 한 메타 데이터가 저장 되는 위치입니다.  리소스 자체는 선택한 모든 인프라에 생성 됩니다. Azure 인프라에 있을 필요는 없습니다.
8. 데이터 컨트롤러의 이름을 입력 합니다.
9. 현재 미리 보기에서는 간접적으로 연결 된 모드만 지원 됩니다.
10. 배포 구성 프로필을 선택 합니다.
11. **Azure Studio에서 열기** 단추를 클릭 합니다.
12. 다음 화면에는 선택 항목에 대 한 요약과 생성 된 노트북이 표시 됩니다.  **프로 비전 된 노트북 다운로드** 단추를 클릭 하 여 노트북을 다운로드할 수 있습니다.
13. Azure Data Studio에서 노트북을 열고 위쪽의 **모두 실행** 단추를 클릭 합니다.
14. 노트북의 프롬프트 및 지침에 따라 데이터 컨트롤러 만들기를 완료 합니다.

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
