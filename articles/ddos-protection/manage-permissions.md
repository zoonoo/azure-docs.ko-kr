---
title: Azure DDoS Protection 계획 권한
description: 보호 계획에서 사용 권한을 관리 하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: dec2c845780b62e9a595c73504037142c511c0e6
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989414"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>DDoS Protection 계획 관리: 사용 권한 및 제한 사항

DDoS 보호 계획은 지역 및 구독에서 작동 합니다. 동일한 계획은 테 넌 트 전체에서 다른 지역의 다른 구독에서 가상 네트워크에 연결할 수 있습니다. 보호된 공용 IP 주소의 수가 100개를 초과하는 경우 계획이 연결된 구독에 따라 계획에 대한 월별 정기 요금과 초과분 요금이 부과됩니다. DDoS 가격 책정에 대한 자세한 내용은 [가격 정보](https://azure.microsoft.com/pricing/details/ddos-protection/)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

- 이 자습서의 단계를 완료 하기 전에 먼저 [Azure DDoS Standard 보호 계획](manage-ddos-protection.md)을 만들어야 합니다.

## <a name="permissions"></a>사용 권한

DDoS 보호 계획을 사용하려면 다음 표에 나열된 적절한 작업이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 계정을 할당해야 합니다.

| 작업                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | DDoS 보호 계획 읽기              |
| Microsoft.Network/ddosProtectionPlans/write       | DDoS 보호 계획 만들기 또는 업데이트  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS 보호 계획 삭제            |
| Microsoft.Network/ddosProtectionPlans/join/action | DDoS 보호 계획에 조인              |

가상 네트워크에 DDoS 보호를 사용하도록 설정하려면 계정에도 [가상 네트워크에 적절한 작업](../virtual-network/manage-virtual-network.md#permissions)을 할당해야 합니다.

## <a name="azure-policy"></a>Azure Policy

대부분의 조직에서는 둘 이상의 계획을 만들 필요가 없습니다. 계획은 구독 간에 이동할 수 없습니다. 계획이 속한 구독을 변경하려면 기존 계획을 삭제하고 새 계획을 만들어야 합니다.

여러 구독을 보유 하 고 있고 비용 제어를 위해 단일 계획을 테 넌 트에 배포 하려는 경우 Azure Policy를 사용 하 여 [Azure DDoS Protection 표준 계획의 생성을 제한할](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy)수 있습니다. 구독이 이전에 예외로 표시 되지 않은 경우이 정책은 DDoS 계획의 생성을 차단 합니다. 또한이 정책은 DDoS 계획을 배포 했지만 준수 하지 않는 모든 구독 목록을 표시 합니다.


## <a name="next-steps"></a>다음 단계

DDoS 보호 계획에 대 한 원격 분석을 보고 구성 하는 방법을 알아보려면 자습서를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [DDoS 보호 원격 분석 보기 및 구성](telemetry-monitoring-alerting.md)