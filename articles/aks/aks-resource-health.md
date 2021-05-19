---
title: AKS 클러스터에 영향을 주는 Resource Health 이벤트 확인(미리 보기)
description: Azure Resource Health를 사용하여 AKS 클러스터의 성능 상태를 확인합니다.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070659"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>AKS 클러스터에 영향을 주는 Resource Health 이벤트 확인(미리 보기)


AKS에서 컨테이너 워크로드를 실행하는 경우 워크로드의 가용성에 대한 영향을 최소화하기 위해 발생하는 즉시 문제를 해결하고 수정하려고 합니다. [Azure Resource Health](../service-health/resource-health-overview.md)를 사용하면 AKS 클러스터를 사용할 수 없게 만드는 다양한 상태 이벤트를 확인할 수 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Resource Health 열기

### <a name="to-access-resource-health-for-your-aks-cluster"></a>AKS 클러스터에 대한 Resource Health에 액세스하려면

- [Azure Portal](https://portal.azure.com)에서 AKS 클러스터로 이동합니다.
- 왼쪽 탐색 영역에서 **리소스 상태** 를 선택합니다.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>구독의 모든 클러스터에 대한 Resource Health에 액세스하려면

- [Azure Portal](https://portal.azure.com)에서 **Service Health** 를 검색하여 이동합니다.
- 왼쪽 탐색 영역에서 **리소스 상태** 를 선택합니다.
- 구독을 선택하고 리소스 종류를 AKS(Azure Kubernetes Service)로 설정합니다.

![AKS 클러스터의 리소스 상태 스크린샷](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>성능 상태 확인

Azure Resource Health를 통해 Azure 리소스에 영향을 주는 서비스 문제를 진단하고 지원을 받을 수 있습니다. Resource Health는 리소스의 현재 및 과거 상태를 보고하고 사용자가 시작한 작업 또는 플랫폼 이벤트로 인한 문제 인지를 확인하는 데 도움이 됩니다.

Resource Health는 관리형 클러스터에 대한 신호를 수신하여 클러스터의 성능 상태를 확인합니다. AKS 클러스터의 성능 상태를 검사하고 각 상태 신호에 필요한 작업을 보고합니다. 해당 신호는 자동 해결 문제, 계획된 업데이트, 계획되지 않은 상태 이벤트, 사용자가 시작한 작업으로 인한 사용 불가능 등 다양합니다. 신호는Azure Resource Health의 성능 상태(‘사용 가능’, ‘사용 불가능’, ‘알 수 없음’, ‘저하됨’)를 사용하여 분류됩니다.   

- **사용 가능**: 클러스터 상태에 영향을 주는 알려진 이슈가 없는 경우 Resource Health에서 클러스터를 ‘사용 가능’으로 보고합니다.

- **사용 불가능**: 클러스터 상태에 영향을 주는 플랫폼 또는 비플랫폼 이벤트가 있는 경우 Resource Health에서 클러스터를 ‘사용 불가능’으로 보고합니다.

- **알 수 없음**: 클러스터의 상태 메트릭에 대한 연결이 일시적으로 끊어진 경우 Resource Health에서 클러스터를 ‘알 수 없음’으로 보고합니다.

- **저하됨**: 작업이 필요한 상태 이슈가 있는 경우 Resource Health에서 클러스터를 ‘저하됨’으로 보고합니다.

각 성능 상태의 의미에 대한 자세한 내용은 [Resource Health 개요](../service-health/resource-health-overview.md#health-status)를 참조하세요.

### <a name="view-historical-data"></a>기록 데이터 보기

**상태 기록** 섹션에서 지난 30일 동안의 Resource Health 기록 정보를 볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

클러스터에 대한 검사를 실행하여 [AKS 진단](./concepts-diagnostics.md)을 통해 클러스터 이슈를 추가로 해결합니다.