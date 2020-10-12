---
title: Azure Kubernetes Service 클러스터 리소스 만들기
titleSuffix: Azure Cognitive Services
description: AKS (Azure Kubernetes Service) 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87298756"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes Service 클러스터 리소스 만들기

1. [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)로 이동 하 고 **만들기**를 선택 합니다.

1. **기본** 탭에서 다음 정보를 입력합니다.

    |설정|값|
    |--|--|
    |Subscription|적합한 구독을 선택합니다.|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택합니다.|
    |Kubernetes 클러스터 이름|이름 (소문자)을 입력 합니다.|
    |지역|근처 위치를 선택합니다.|
    |Kubernetes 버전|로 표시 되는 모든 값 **(기본값)** 입니다.|
    |DNS 이름 접두사|자동으로 만들어지지만를 재정의할 수 있습니다.|
    |노드 크기|표준 D S 2 v2:<br>`2 vCPUs`, `7 GB`|
    |노드 수|슬라이더를 기본값으로 둡니다.|

1. **노드 풀** 탭에서 **가상 노드** 및 **VM 크기 집합** 을 기본값으로 설정 된 채로 둡니다.
1. **인증** 탭에서 **서비스 주체** 를 그대로 사용 하 고 **RBAC** 를 기본값으로 설정 합니다.
1. **네트워킹** 탭에서 다음 항목을 입력 합니다.

    |설정|값|
    |--|--|
    |HTTP 애플리케이션 라우팅|아니요|
    |네트워킹 구성|Basic|

1. **통합** 탭에서 **컨테이너 모니터링** 이 **사용**으로 설정 되어 있는지 확인 하 고 **Log Analytics 작업 영역** 을 기본값으로 둡니다.
1. **태그** 탭에서 이름/값 쌍을 비워 둡니다.
1. **검토 및 만들기를**선택 합니다.
1. 유효성 검사를 통과 한 후 **만들기**를 선택 합니다.

> [!NOTE]
> 유효성 검사가 실패 하는 경우 "서비스 주체" 오류 때문일 수 있습니다. **인증** 탭으로 돌아가서 유효성 검사를 실행 하 고를 전달 하는 **검토 + 만들기**로 돌아갑니다.
