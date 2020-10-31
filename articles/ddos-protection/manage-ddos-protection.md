---
title: Azure Portal을 사용하여 Azure DDoS Protection 표준 관리
description: Azure DDoS Protection 표준을 사용 하 여 공격을 완화 하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: fc60ca462a2891cc022847e056e32239f2675f70
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094577"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>빠른 시작: Azure DDoS Protection Standard 만들기 및 구성

Azure Portal를 사용 하 여 Azure DDoS Protection Standard를 시작 합니다. 

DDoS 보호 계획은 구독 전반에 걸쳐 DDoS 보호 표준을 사용하도록 설정된 일단의 가상 네트워크를 정의합니다. 조직에 대해 하나의 DDoS 보호 계획을 구성하고 여러 구독의 가상 네트워크를 동일한 계획에 연결할 수 있습니다. 

이 빠른 시작에서는 DDoS 보호 계획을 만들고 가상 네트워크에 연결 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다. 계정이 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용 권한에](manage-permissions.md)대 한 방법 가이드에 나열 된 적절 한 작업에 할당 된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 할당 되었는지 확인 합니다.

## <a name="create-a-ddos-protection-plan"></a>DDoS 보호 계획 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
2. *DDoS* 용어를 검색 합니다. **DDoS 보호 계획이** 검색 결과에 표시 되 면 선택 합니다.
3. **만들기** 를 선택합니다.
4. 다음 값을 입력 하거나 선택한 다음, **만들기** 를 선택 합니다.

    |설정        |값                                              |
    |---------      |---------                                          |
    |Name           | _MyDdosProtectionPlan_ 를 입력 합니다.                     |
    |Subscription   | 구독을 선택합니다.                         |
    |Resource group | **새로 만들기** 를 선택 하 고 _myresourcegroup_ 을 입력 합니다.|
    |위치       | _미국 동부_ 를 입력 합니다.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>가상 네트워크에 대 한 DDoS 보호 사용

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>새 가상 네트워크에 대 한 DDoS 보호 사용

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
2. **네트워킹** 을 선택한 다음 **가상 네트워크** 를 선택 합니다.
3. 다음 값을 입력 하거나 선택 하 고 나머지 기본값을 적용 한 다음 **만들기** 를 선택 합니다.

    | 설정         | 값                                           |
    | ---------       | ---------                                       |
    | Name            | _Myvnet_ 을 입력 합니다.                                 |
    | Subscription    | 구독을 선택합니다.                                    |
    | Resource group  | **기존 사용** 을 선택 하 고 **myresourcegroup** 을 선택 합니다. |
    | 위치        | _미국 동부_ 입력                                                    |
    | DDoS Protection 표준 | **사용** 을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.|

가상 네트워크에 대해 DDoS 표준을 사용하도록 설정하면 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다. DDoS 표준을 사용하도록 설정된 가상 네트워크를 이동해야 하는 경우 먼저 DDoS 표준을 사용하지 않도록 설정하고, 가상 네트워크를 이동한 다음, DDoS 표준을 사용하도록 설정합니다. 이동 후에는 가상 네트워크의 모든 보호된 공용 IP 주소에 대한 자동 조정된 정책 임계값이 다시 설정됩니다.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>기존 가상 네트워크에 대해 DDoS protection 사용

1. 기존 DDoS 보호 계획이 없는 경우 [DDoS 보호 계획 만들기](#create-a-ddos-protection-plan)의 단계를 완료하여 DDoS 보호 계획을 만듭니다.
2. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
3. 포털 위쪽의 **리소스, 서비스 및 문서 검색 상자** 에서 DDoS 보호 표준을 사용하도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
4. **설정** 아래에서 **DDoS 보호** 를 선택합니다.
5. **표준** 을 선택합니다. **DDoS 보호 계획** 아래에서 기존 DDoS 보호 계획 또는 1단계에서 만든 계획을 선택한 다음, **저장** 을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.

## <a name="validate-and-test"></a>유효성 검사 및 테스트

먼저 DDoS 보호 계획의 세부 정보를 확인 합니다.

1. 포털의 왼쪽 위에서 **모든 서비스** 를 선택합니다.
2. **필터** 상자에서 *DDoS* 를 입력합니다. **DDoS 보호 계획** 이 결과에 표시되면 선택합니다.
3. 목록에서 DDoS 보호 계획을 선택 합니다.

_Myvnet_ 가상 네트워크가 나열 됩니다. 

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서에 대 한 리소스를 유지할 수 있습니다. 더 이상 필요 하지 않은 경우 _Myresourcegroup_ 리소스 그룹을 삭제 합니다. 리소스 그룹을 삭제 하면 DDoS 보호 계획과 관련 된 모든 리소스도 삭제 됩니다. 이 DDoS 보호 계획을 사용 하지 않으려면 불필요 한 요금을 방지 하기 위해 리소스를 제거 해야 합니다.

   >[!WARNING]
   >이 작업은 되돌릴 수 없습니다.

1. Azure Portal에서 **리소스 그룹** 을 검색하여 선택하거나 Azure Portal 메뉴에서 **리소스 그룹** 을 선택합니다.

2. 필터 또는 아래로 스크롤하여 _Myresourcegroup_ 리소스 그룹을 찾습니다.

3. 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

4. 확인할 리소스 그룹 이름을 입력한 다음, **삭제** 를 선택합니다.

가상 네트워크에 대 한 DDoS 보호를 사용 하지 않도록 설정 하려면: 

1. 포털 위쪽의 **리소스, 서비스 및 문서 검색 상자** 에서 DDoS 보호 표준을 사용하지 않도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
2. **DDoS Protection Standard에서** 를 선택 하 고 **사용 안 함** 을 선택 합니다.

DDoS 보호 계획을 삭제 하려면 먼저 모든 가상 네트워크를 분리 해야 합니다. 

## <a name="next-steps"></a>다음 단계

DDoS 보호 계획에 대 한 원격 분석을 보고 구성 하는 방법을 알아보려면 자습서를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [DDoS 보호 원격 분석 보기 및 구성](telemetry-monitoring-alerting.md)
