---
title: Azure에 클라우드 서비스(클래식)를 배포할 때 ConstrainedAllocationFailed 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure에 클라우드 서비스(클래식)를 배포할 때 ConstrainedAllocationFailed 예외를 해결하는 방법을 보여 줍니다.
services: cloud-services
author: hirenshah1
ms.author: hirshah
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 2b491560af61bc0afc8696d45c32ed91b846a9e4
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090028"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Azure에 클라우드 서비스(클래식)를 배포할 때 ConstrainedAllocationFailed 문제 해결

이 문서에서는 할당 제약 조건 때문에 Azure 클라우드 서비스(클래식)를 배포할 수 없는 할당 오류를 해결합니다.

클라우드 서비스(클래식)에 인스턴스를 배포하거나 새 웹 또는 작업자 역할 인스턴스를 추가할 때 Microsoft Azure는 컴퓨팅 리소스를 할당합니다.

Azure 구독 제한에 도달하기 전에도 이러한 작업 중에 간혹 오류가 발생할 수 있습니다.

> [!TIP]
> 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

## <a name="symptom"></a>증상

Azure Portal에서 클라우드 서비스(클래식)로 이동하여 사이드바에서 ‘작업 로그(클래식)’를 선택하고 로그를 확인합니다.

![작업 로그(클래식) 블레이드를 보여 주는 이미지입니다.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

클라우드 서비스(클래식)의 로그를 검사하는 경우 다음과 같은 예외가 표시됩니다.

|예외 유형  |오류 메시지  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure 작업 ‘`{Operation ID}`’가 실패하고 Compute.ConstrainedAllocationFailed 코드가 반환되었습니다. 세부 정보: 할당에 실패했습니다. 요청의 제약 조건을 충족할 수 없습니다. 요청된 새로운 서비스 배포가 선호도 그룹에 바인딩되어 있거나 가상 네트워크를 대상으로 하거나 호스티드 서비스에 기존 배포가 있습니다. 이러한 조건이 특정 Azure 리소스에 대한 새로운 배포를 제한합니다. 나중에 다시 시도하거나 VM 크기 또는 역할 인스턴스 수를 줄여 보세요. 가능하다면 앞서 말한 제약 조건을 제거하거나 다른 지역에 배포를 시도합니다.|

## <a name="cause"></a>원인

첫 번째 인스턴스가 클라우드 서비스에 배포되면(스테이징 또는 프로덕션 단계) 해당 클라우드 서비스가 클러스터에 고정됩니다.

시간이 지남에 따라 이 클러스터의 리소스가 완전히 활용될 수 있습니다. 고정된 클러스터에서 사용할 수 있는 리소스가 부족한 경우 클라우드 서비스(클래식)에서 추가 리소스에 대한 할당 요청을 수행하면 할당 오류가 발생합니다. 자세한 내용은 [할당 오류의 일반적인 문제](cloud-services-allocation-failures.md#common-issues)를 참조하세요.

## <a name="solution"></a>해결 방법

기존 클라우드 서비스는 클러스터에 ‘고정’되어 있습니다. 동일한 클러스터에서 클라우드 서비스(클래식)에 대한 추가 배포가 수행됩니다.

이 시나리오에서 할당 오류가 발생하는 경우 권장되는 작업 과정은 새 클라우드 서비스(클래식)에 다시 배포하고 *CNAME* 을 업데이트하는 것입니다.

> [!TIP]
> 이 솔루션은 플랫폼이 해당 영역의 모든 클러스터에서 선택할 수 있으므로 가장 성공률이 높습니다.

> [!NOTE]
> 이 솔루션은 가동 중지 시간 없이 발생합니다.

1. 새 클라우드 서비스(클래식)에 워크로드를 배포합니다.
    - 추가 지침은 [클라우드 서비스(클래식)를 만들고 배포하는 방법](cloud-services-how-to-create-deploy-portal.md) 가이드를 참조하세요.

    > [!WARNING]
    > 이 배포 슬롯에 연결된 IP 주소가 손실되지 않도록 하려면 [해결 방법 3 - IP 주소 유지](cloud-services-allocation-failures.md#solutions)를 사용할 수 있습니다.

1. *CNAME* 또는 *A* 레코드를 업데이트하여 트래픽이 새 클라우드 서비스(클래식)로 향하도록 합니다.
    - 추가 지침은 [Azure 클라우드 서비스(클래식)에 대한 사용자 지정 도메인 이름 구성](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) 가이드를 참조하세요.

1. 제로(0) 트래픽이 이전 사이트로 이동하면 이전 클라우드 서비스(클래식)를 삭제할 수 있습니다.
    - 추가 지침은 [배포 및 클라우드 서비스(클래식) 삭제](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) 가이드를 참조하세요.
    - 클라우드 서비스(클래식)의 네트워크 트래픽을 확인하려면 [클라우드 서비스(클래식) 모니터링 소개](cloud-services-how-to-monitor.md)를 참조하세요.

추가 수정 단계는 [클라우드 서비스(클래식) 할당 오류 문제 해결 | Microsoft Docs](cloud-services-allocation-failures.md#common-issues)를 참조하세요.

## <a name="next-steps"></a>다음 단계

할당 오류 해결 방법 및 배경 정보에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [할당 오류 - 클라우드 서비스(클래식)](cloud-services-allocation-failures.md)

현재 Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 Stack Overflow](https://azure.microsoft.com/support/forums/)에서 Azure 포럼에 방문하세요. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. 또한 Azure 지원 요청을 제출할 수 있습니다. 지원 요청을 제출하려면 [Azure 지원](https://azure.microsoft.com/support/options/) 페이지에서 *지원 받기* 를 선택합니다.