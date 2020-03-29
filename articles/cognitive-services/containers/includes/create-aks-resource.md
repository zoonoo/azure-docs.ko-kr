---
title: Azure Kubernetes 서비스 클러스터 리소스 만들기
titleSuffix: Azure Cognitive Services
description: AKS(Azure Kubernetes) 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383466"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes 서비스 클러스터 리소스 만들기

1. Azure [Kubernetes 서비스로](https://ms.portal.azure.com/#create/microsoft.aks)이동하여 **만들기를**선택합니다.

1. 기본 탭에서 다음 정보를 **입력합니다.**

    |설정|값|
    |--|--|
    |Subscription|적합한 구독을 선택합니다.|
    |Resource group|사용 가능한 리소스 그룹을 선택합니다.|
    |쿠버네츠 클러스터 이름|이름(소문자)을 입력합니다.|
    |지역|주변 위치를 선택합니다.|
    |쿠베르네테스 버전|어떤 값이든 **(기본값)으로**표시됩니다.|
    |DNS 이름 접두사|자동으로 생성되지만 재정의할 수 있습니다.|
    |노드 크기|표준 DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |노드 수|슬라이더를 기본값으로 둡니다.|

1. **축척** 탭에서 **가상 노드** 및 **VM 축척 세트를** 기본값으로 설정한 상태로 둡니다.
1. **인증** 탭에서 **서비스 주체를** 두고 RBAC 를 기본값으로 **설정합니다.**
1. **네트워킹** 탭에서 다음 선택 항목을 입력합니다.

    |설정|값|
    |--|--|
    |HTTP 애플리케이션 라우팅|예|
    |네트워킹 구성|Basic|

1. **모니터링** 탭에서 **컨테이너 모니터링 사용이** **예로**설정되어 있는지 확인하고 Log Analytics **작업 영역을** 기본값으로 둡니다.
1. **태그** 탭에서 이름/값 쌍을 지금은 비워 둡니다.
1. **검토 및 만들기를 선택합니다.**
1. 유효성 검사가 통과되면 **만들기를**선택합니다.

> [!NOTE]
> 유효성 검사가 실패하면 "서비스 주체" 오류 때문일 수 있습니다. **인증** 탭으로 돌아가서 유효성 검사를 실행한 다음 전달해야 하는 **검토 + 만들기로**돌아갑니다.
