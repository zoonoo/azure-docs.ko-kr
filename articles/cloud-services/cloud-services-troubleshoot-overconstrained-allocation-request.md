---
title: Azure에 클라우드 서비스(클래식)를 배포할 때 OverconstrainedAllocationRequest 문제 해결 | Microsoft Docs
description: 이 문서에서는 클라우드 서비스(클래식)를 Azure에 배포할 때 OverconstrainedAllocationRequest 예외 사항을 해결하는 방법을 보여 줍니다.
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745637"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>클라우드 서비스(클래식)를 Azure에 배포할 때 OverconstrainedAllocationRequest 문제 해결

이 문서에서는 Azure Cloud Services(클래식) 배포를 방해하는 제약에 의한 할당 실패를 해결해 봅니다.

클라우드 서비스에 인스턴스를 배포하거나 새 웹 또는 작업자 역할 인스턴스를 추가할 때 Microsoft Azure는 컴퓨팅 리소스를 할당합니다.

Azure 구독 제한에 도달하기 전에도 이러한 작업 중에 간혹 오류가 발생할 수 있습니다.

> [!TIP]
> 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

## <a name="symptom"></a>증상

![이미지는 작업 로그(클래식) 블레이드를 보여 줍니다.](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|예외 유형  |오류 메시지  |
|---------|---------|
|OverconstrainedAllocationRequest |배포 요청 제약 조건으로 인해 배포에 요구되는 VM 크기(또는 VM 크기의 조합)를 프로비전할 수 없습니다. 가능한 경우, 가상 네트워크 바인딩 같은 제약 조건을 해제하고 다른 배포를 포함하지 않는 호스티드 서비스에 배포를 시도하거나 다른 선호도 그룹 또는 선호도 그룹 없이 배포를 시도하거나 다른 지역으로 배포를 시도합니다.|

## <a name="cause"></a>원인

클라우드 서비스가 **고정됨** 상태인지 또는 **고정 해제됨** 상태인지에 따라 근본 원인이 달라집니다.

- [**고정 해제됨:** 새 클라우드 서비스(클래식)의 첫 번째 배포 실패](#not-pinned-to-a-cluster)
- [**고정됨:** 기존 클라우드 서비스에서 발생하는 오류 (클래식)](#pinned-to-a-cluster)

> [!NOTE]
> 첫 번째 인스턴스가 클라우드 서비스(스테이징 또는 프로덕션)에 배포되면 해당 클라우드 서비스가 클러스터에 고정됩니다.
>
> 시간이 지남에 따라 클러스터의 리소스가 완전히 활용될 수 있습니다. 고정된 클러스터에서 사용할 수 있는 리소스가 부족한 경우 클라우드 서비스(클래식)에서 추가 리소스에 대한 할당 요청을 수행하면 이 요청으로 인해 [할당 오류](cloud-services-allocation-failures.md)가 발생합니다.

## <a name="solution"></a>솔루션

다음 시나리오에서 할당 오류에 대한 지침을 따르세요.

### <a name="not-pinned-to-a-cluster"></a>클러스터에 고정되지 않음

클라우드 서비스(클래식)를 처음 배포할 때 클러스터가 아직 선택되지 않았기 때문에 클라우드 서비스가 *고정* 되지 않습니다. 다음과 같은 이유로 Azure에서 배포 오류가 발생할 수 있습니다.

- 해당 지역에서 사용할 수 없는 특정 크기를 선택했습니다.
- 다른 역할에 필요한 크기의 조합을 해당 지역에서 사용할 수 없습니다.

이 시나리오에서 할당 오류가 발생하는 경우 권장되는 작업은 지역에서 사용 가능한 크기를 확인하고 이전에 지정한 크기를 변경하는 것입니다.

1. [클라우드 서비스(클래식) 제품](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) 페이지에서는 지역에서 사용할 수 있는 크기를 확인할 수 있습니다.

    > [!NOTE]
    > *제품* 페이지에 사용 가능한 용량이 표시되지 않습니다. 새 할당의 경우 Azure는 해당 시점에 해당 지역에서 최적의 클러스터를 선택할 수 있어야 합니다.

1. 클라우드 서비스(클래식)에 대한 서비스 정의 파일을 업데이트하여 지역에서 다른 [제품 크기](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services)를 지정합니다.

### <a name="pinned-to-a-cluster"></a>클러스터에 고정됨

기존 클라우드 서비스는 클러스터에 *고정* 됩니다. 클라우드 서비스(클래식)에 대한 추가 배포는 동일한 클러스터에서 수행됩니다.

이 시나리오에서 할당 오류가 발생하는 경우 권장되는 작업 과정은 새 클라우드 서비스(클래식)에 다시 배포하고 *CNAME* 을 업데이트하는 것입니다.

> [!TIP]
> 이 솔루션은 플랫폼이 해당 영역의 모든 클러스터에서 선택할 수 있으므로 가장 성공률이 높습니다.

> [!NOTE]
> 이 솔루션은 가동 중지 시간 없이 발생합니다.

1. 새 클라우드 서비스(클래식)에 워크로드를 배포합니다.
    - 추가 지침은 [클라우드 서비스(클래식)를 만들고 배포하는 방법](cloud-services-how-to-create-deploy-portal.md) 가이드를 참조하세요.

    > [!WARNING]
    > 이 배포 슬롯에 연결된 IP 주소를 잃지 않으려면 [솔루션 3 - IP 주소 유지](cloud-services-allocation-failures.md#solutions)를 사용할 수 있습니다.

1. 새 클라우드 서비스(클래식)로의 트래픽을 가리키도록 *CNAME* 또는 *A* 레코드를 업데이트합니다.
    - 추가 지침은 [Azure 클라우드 서비스(클래식)에 대한 사용자 지정 도메인 이름 구성](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) 가이드를 참조하세요.

1. 제로(0) 트래픽이 이전 사이트로 이동하면 이전 클라우드 서비스(클래식)를 삭제할 수 있습니다.
    - 추가 지침은 [배포 및 클라우드 서비스(클래식) 삭제](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) 가이드를 참조하세요.
    - 클라우드 서비스(클래식)의 네트워크 트래픽을 확인하려면 [클라우드 서비스(클래식) 모니터링 소개](cloud-services-how-to-monitor.md)를 참조하세요.

추가 수정 단계에 대해서는 [클라우드 서비스(클래식) 할당 오류 문제 해결 | Microsoft Docs](cloud-services-allocation-failures.md#common-issues)를 참조하세요.

## <a name="next-steps"></a>다음 단계

추가 할당 오류 솔루션 및 배경 정보는 다음과 같습니다.

> [!div class="nextstepaction"]
> [할당 오류 - 클라우드 서비스(클래식)](cloud-services-allocation-failures.md)

Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 Stack Overflow에서 Azure 포럼](https://azure.microsoft.com/support/forums/)을 방문합니다. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. 또한 Azure 지원 요청을 제출할 수 있습니다. 지원 요청을 제출하려면 [Azure 지원](https://azure.microsoft.com/support/options/) 페이지에서 *지원 받기* 를 선택합니다.
