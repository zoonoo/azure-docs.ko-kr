---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure는 kubernetes (AKS) 리소스를 만드는 방법에 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: eb2d609c3a7dfd769c6a19854366ac5e45fddb7d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712583"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Azure Kubernetes Service (AKS) 클러스터 리소스 만들기

1. 로 이동 합니다 [만들](https://ms.portal.azure.com/#create/microsoft.aks) Kubernetes 서비스에 대 한 합니다.

1. 에 **기본 사항** 탭에서 다음 세부 정보를 입력 합니다.

    |설정|값|
    |--|--|
    |구독|적절 한 구독을 선택 합니다.|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택 합니다.|
    |Kubernetes 클러스터 이름|원하는 이름 (소문자)|
    |Region|가까운 위치를 선택 합니다.|
    |Kubernetes 버전|1.12.8 (기본값)|
    |DNS 이름 접두사|자동으로 생성 되지만 선택적으로 재정의할 수|
    |노드 크기|표준 DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |노드 수|기본 값에서 슬라이더를 그대로 둡니다.|

1. 에 **확장** 탭을 합니다 *가상 노드* 및 *가상 머신 확장 집합* 기본값입니다.
1. 에 **인증** 탭에서 *서비스 주체* 하 고 *RBAC를 사용 하도록 설정* 기본값.
1. 에 **네트워킹** 탭에서 다음 선택 항목을 입력 합니다.

    |설정|값|
    |--|--|
    |HTTP 애플리케이션 라우팅|아니요|
    |네트워킹 구성|Basic|

1. 에 **모니터링** 탭에서 *컨테이너 모니터링 사용* 로 설정 되어 **예** 두고 합니다 *Log Analytics 작업 영역* 으로 해당 기본값
1. 에 **태그** 탭, 지금은 이름/값 쌍을 비워 둡니다.
1. 클릭 **검토 및 만들기**합니다.
1. 유효성 검사에 통과 한 후 클릭 **만들기**합니다.

> [!NOTE]
> 유효성 검사에 실패할 경우 때문일 수 있습니다는 *서비스 주체* 오류입니다. 로 다시 이동 합니다 *인증* 탭을 선택한 다음 다시 *검토 + 만들기* 유효성 검사는 실행 하 고 전달 합니다.
