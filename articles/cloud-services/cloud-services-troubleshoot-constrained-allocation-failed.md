---
title: Azure에 클라우드 서비스 (클래식)를 배포할 때 ConstrainedAllocationFailed 문제 해결 | Microsoft Docs
description: 이 문서에서는 클라우드 서비스 (클래식)를 Azure에 배포할 때 ConstrainedAllocationFailed 예외를 해결 하는 방법을 보여 줍니다.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738289"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Azure에 클라우드 서비스 (클래식)를 배포할 때 ConstrainedAllocationFailed 문제 해결

이 문서에서는 할당 제약 조건 때문에 Azure Cloud services (클래식)를 배포할 수 없는 할당 오류를 해결 합니다.

클라우드 서비스 (클래식)에 인스턴스를 배포 하거나 새 웹 또는 작업자 역할 인스턴스를 추가 하는 경우 Microsoft Azure 계산 리소스를 할당 합니다.

Azure 구독 제한에 도달 하기 전에도 이러한 작업 중에 오류가 발생할 수 있습니다.

> [!TIP]
> 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

## <a name="symptom"></a>증상

Azure Portal에서 클라우드 서비스 (클래식)로 이동 하 고 사이드바에서 *작업 로그 (클래식)* 를 선택 하 여 로그를 확인 합니다.

![이미지 작업 로그 (클래식) 블레이드를 표시 합니다.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

클라우드 서비스 (클래식)의 로그를 검사 하는 경우 다음과 같은 예외가 표시 됩니다.

|예외 유형  |오류 메시지  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure 작업 ' `{Operation ID}` '이 (가) ConstrainedAllocationFailed 코드와 함께 실패 했습니다. 세부 정보: 할당 하지 못했습니다. 요청에서 제약 조건을 충족할 수 없습니다. 요청된 새로운 서비스 배포가 선호도 그룹에 바인딩되어 있거나 가상 네트워크를 대상으로 하거나 호스티드 서비스에 기존 배포가 있습니다. 이러한 조건이 특정 Azure 리소스에 대한 새로운 배포를 제한합니다. 나중에 다시 시도 하거나, VM 크기나 역할 인스턴스의 수를 줄여 보세요. 가능하다면 앞서 말한 제약 조건을 제거하거나 다른 지역에 배포를 시도합니다.|

## <a name="cause"></a>원인

첫 번째 인스턴스가 클라우드 서비스 (스테이징 또는 프로덕션)에 배포 되 면 해당 클라우드 서비스는 클러스터에 고정 됩니다.

시간이 지남에 따라이 클러스터의 리소스가 완전히 활용 될 수 있습니다. 고정 된 클러스터에서 사용할 수 있는 리소스가 부족 한 경우 클라우드 서비스 (클래식)에서 추가 리소스에 대 한 할당 요청을 수행 하면 요청에 의해 할당 오류가 발생 합니다. 자세한 내용은 [할당 오류 일반적인 문제](cloud-services-allocation-failures.md#common-issues)를 참조 하세요.

## <a name="solution"></a>솔루션

기존 클라우드 서비스는 클러스터에 *고정* 됩니다. 클라우드 서비스 (클래식)에 대 한 추가 배포는 동일한 클러스터에서 수행 됩니다.

이 시나리오에서 할당 오류가 발생 하는 경우 권장 되는 작업 과정은 새 클라우드 서비스 (클래식)에 다시 배포 하 고 *CNAME* 을 업데이트 하는 것입니다.

> [!TIP]
> 이 솔루션은 플랫폼이 해당 영역의 모든 클러스터에서 선택할 수 있으므로 가장 성공률이 높습니다.

> [!NOTE]
> 이 솔루션은 가동 중지 시간 없이 발생합니다.

1. 새 클라우드 서비스 (클래식)에 워크 로드를 배포 합니다.
    - 추가 지침은 [클라우드 서비스를 만들고 배포 하는 방법 (클래식)](cloud-services-how-to-create-deploy-portal.md) 가이드를 참조 하세요.

    > [!WARNING]
    > 이 배포 슬롯에 연결 된 IP 주소를 잃지 않으려면 [솔루션 3-ip 주소 유지](cloud-services-allocation-failures.md#solutions)를 사용할 수 있습니다.

1. 새 클라우드 서비스 (클래식)에 대 한 트래픽을 가리키도록 *CNAME* 또는 *A* 레코드를 업데이트 합니다.
    - 추가 지침은 [Azure 클라우드 서비스에 대 한 사용자 지정 도메인 이름 구성 (클래식)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) 가이드를 참조 하세요.

1. 0 개의 트래픽이 이전 사이트로 이동 하면 이전 클라우드 서비스 (클래식)를 삭제할 수 있습니다.
    - 추가 지침은 [배포 및 클라우드 서비스 삭제 (클래식)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) 가이드를 참조 하세요.
    - 클라우드 서비스 (클래식)의 네트워크 트래픽을 확인 하려면 [클라우드 서비스 (클래식) 모니터링 소개](cloud-services-how-to-monitor.md)를 참조 하세요.

[Cloud service (클래식) 할당 오류 문제 해결을 참조 하세요. ](cloud-services-allocation-failures.md#common-issues)추가 수정 단계를 Microsoft Docs 합니다.

## <a name="next-steps"></a>다음 단계

추가 할당 오류 솔루션 및 배경 정보는 다음과 같습니다.

> [!div class="nextstepaction"]
> [할당 오류-클라우드 서비스 (클래식)](cloud-services-allocation-failures.md)

이 문서에서 Azure 문제가 해결 되지 않으면 MSDN에서 Azure 포럼을 방문 [하 여 Stack Overflow](https://azure.microsoft.com/support/forums/)합니다. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. 또한 Azure 지원 요청을 제출할 수 있습니다. 지원 요청을 제출하려면 [Azure 지원](https://azure.microsoft.com/support/options/) 페이지에서 *지원 받기* 를 선택합니다.