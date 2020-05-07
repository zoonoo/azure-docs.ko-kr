---
title: 일반 배포 오류 문제 해결
titleSuffix: Azure Load Balancer
description: Azure 부하 분산 장치를 배포할 때 일반적인 오류를 해결 하는 방법을 설명 합니다.
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 26b4232832589827a09774eb0106c25e5c446eb1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791088"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Azure Load Balancer와 관련 된 일반적인 Azure 배포 오류 해결

이 문서에서는 몇 가지 일반적인 Azure Load Balancer 배포 오류를 설명 하 고 오류를 해결 하기 위한 정보를 제공 합니다. 이 문서에서 오류 코드에 대 한 정보를 찾고 해당 정보를 제공 하지 않는 경우 알려주세요. 이 페이지의 맨 아래에서 피드백을 남길 수 있습니다. 사용자 의견은 GitHub 문제를 통해 추적 됩니다.

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 세부 정보 및 완화 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| 공용 IP SKU와 Load Balancer SKU가 모두 일치 해야 합니다. Azure Load Balancer 및 공용 IP Sku가 일치 하는지 확인 합니다. 표준 SKU는 프로덕션 워크 로드에 권장 됩니다. [Sku의 차이점](./skus.md) 에 대해 자세히 알아보기  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | 가상 머신 확장 집합은 SKU가 지정 되지 않았거나 표준 공용 Ip 없이 배포 된 경우 기본적으로 기본 부하 분산 장치로 설정 됩니다. 개별 인스턴스에 표준 공용 Ip를 사용 하 여 가상 머신 확장 집합을 다시 배포 하 여 표준 Load Balancer 선택 되었는지 확인 하거나 Azure Portal에서 가상 머신 확장 집합을 배포할 때 표준 LB를 선택 하기만 하면 됩니다. |
|MaxAvailabilitySetsInLoadBalancerReached | Load Balancer의 백 엔드 풀에는 최대 150의 가용성 집합이 포함 될 수 있습니다. 백 엔드 풀의 Vm에 대해 명시적으로 정의 된 가용성 집합이 없는 경우 각 단일 VM은 자체 가용성 집합으로 이동 합니다. 따라서 150 독립 실행형 Vm을 배포 하는 것은 150 가용성 집합을 포함 하 여 한도에 도달 하는 것을 의미 합니다. 가용성 집합을 배포 하 고이에 대 한 추가 Vm을 추가 하 여 해결할 수 있습니다. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 동일한 가상 머신 확장 집합에서 참조 하는 동일한 백 엔드 포트 및 프로토콜을 사용 하 여 지정 된 부하 분산 장치 유형 (내부, 공용)에 둘 이상의 규칙을 가질 수 없습니다. 규칙을 업데이트 하 여이 중복 규칙 만들기를 변경 합니다. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 동일한 가상 머신 확장 집합에서 참조 하는 동일한 백 엔드 포트 및 프로토콜을 사용 하 여 지정 된 부하 분산 장치 유형 (내부, 공용)에 둘 이상의 규칙을 가질 수 없습니다. 규칙 매개 변수를 업데이트 하 여이 중복 규칙 만들기를 변경 합니다. |
|AnotherInternalLoadBalancerExists| Load Balancer의 백 엔드에서 동일한 Vm/네트워크 인터페이스 집합을 내부 참조 유형 Load Balancer 하나만 사용할 수 있습니다. 동일한 유형의 Load Balancer 하나만 만들도록 배포를 업데이트 합니다. |
|CannotUseInactiveHealthProbe| 가상 머신 확장 집합 상태에 대해 구성 된 규칙에서는 사용 하지 않는 프로브를 사용할 수 없습니다. 설정 된 프로브를 적극적으로 사용 중인지 확인 합니다. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 동일한 유형의 부하 분산 장치를 여러 개 포함할 수 없습니다 (내부, 공용). 내부 및 공용 Load Balancer를 한 개까지 포함할 수 있습니다. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | 다중 배치 그룹 가상 머신 확장 집합 또는 가용성 영역 가상 머신 확장 집합에 대 한 기본 Load Balancer 지원 되지 않습니다. 대신 표준 Load Balancer를 사용 해야 합니다. |
|ResourceDeploymentFailure| 부하 분산 장치가 실패 상태에 있는 경우 다음 단계에 따라 실패 상태에서 다시 가져옵니다.<ol><li>https://resources.azure.com으로 이동 하 여 Azure Portal 자격 증명으로 로그인 합니다.</li><li>**읽기/쓰기**를 선택 합니다.</li><li>왼쪽에서 **구독**을 확장 한 다음 업데이트할 Load Balancer를 사용 하 여 구독을 확장 합니다.</li><li>**Resourcegroups**를 확장 한 다음 업데이트할 Load Balancer를 사용 하 여 리소스 그룹을 확장 합니다.</li><li>**Microsoft. 네트워크** > **loadbalancers 조정기**를 선택 하 고 업데이트할 Load Balancer를 선택 **LoadBalancer_1**합니다.</li><li>**LoadBalancer_1**에 대 한 표시 페이지에서 편집 **가져오기** > **Edit**를 선택 합니다.</li><li>**ProvisioningState** 값을 **Failed** 에서 **Succeeded**로 업데이트 합니다.</li><li>**PUT**을 선택합니다.</li></ol>|
|  |  |

## <a name="next-steps"></a>다음 단계

* Azure Load Balancer [SKU 비교 표](./skus.md) 살펴보기
* [Azure Load Balancer 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) 에 대해 알아보기
