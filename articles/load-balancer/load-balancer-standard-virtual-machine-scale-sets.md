---
title: Azure 표준 Load Balancer 및 가상 머신 확장 집합에 대 한 규칙 추가
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: 이 학습 경로를 사용 하 여 Azure 표준 Load Balancer 및 가상 머신 확장 집합을 시작 합니다.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592282"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>가상 머신 확장 집합을 사용 하 여 Azure Load Balancer에 대 한 규칙 추가

가상 머신 확장 집합 및 Azure Load Balancer 작업 하는 경우 다음 지침을 고려 하세요.

## <a name="port-forwarding-and-inbound-nat-rules"></a>포트 전달 및 인바운드 NAT 규칙

확장 집합을 만든 후에는 부하 분산 장치의 상태 프로브에서 사용 하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다. 포트를 변경 하려면 가상 머신 확장 집합을 업데이트 하 고 포트를 업데이트 하 여 상태 프로브를 제거 합니다. 그런 다음 상태 프로브를 다시 구성 합니다.

부하 분산 장치의 백 엔드 풀에 있는 가상 머신 확장 집합을 사용 하는 경우 기본 인바운드 NAT 규칙이 자동으로 만들어집니다.
  
## <a name="inbound-nat-pool"></a>인바운드 NAT 풀

각 가상 머신 확장 집합에는 하나 이상의 인바운드 NAT 풀이 있어야 합니다. 인바운드 NAT 풀은 인바운드 NAT 규칙의 컬렉션입니다. 하나의 인바운드 NAT 풀에서 여러 가상 머신 확장 집합을 지원할 수 없습니다.

## <a name="load-balancing-rules"></a>부하 분산 규칙

부하 분산 장치의 백 엔드 풀에 있는 가상 머신 확장 집합을 사용 하는 경우 기본 부하 분산 규칙이 자동으로 만들어집니다.
  
## <a name="outbound-rules"></a>아웃바운드 규칙

부하 분산 규칙에서 이미 참조 하는 백 엔드 풀에 대 한 아웃 바운드 규칙을 만들려면 인바운드 부하 분산 규칙을 만들 때 Azure Portal에서 **암시적 아웃 바운드 규칙 만들기** 에서 **아니요** 를 선택 합니다.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="부하 분산 규칙 생성을 보여 주는 스크린샷" border="true":::

기존 Load Balancer 인스턴스를 사용 하 여 가상 머신 확장 집합을 배포 하려면 다음 메서드를 사용 합니다.

* [Azure Portal를 사용 하 여 기존 Azure Load Balancer 인스턴스를 사용 하 여 가상 머신 확장 집합 구성](./configure-vm-scale-set-portal.md)
* [Azure PowerShell를 사용 하 여 기존 Azure Load Balancer 인스턴스를 사용 하 여 가상 머신 확장 집합 구성](./configure-vm-scale-set-powershell.md)
* [Azure CLI를 사용 하 여 기존 Azure Load Balancer 인스턴스를 사용 하 여 가상 머신 확장 집합 구성](./configure-vm-scale-set-cli.md)
* [가상 컴퓨터 확장 집합에서 사용 하는 Azure Load Balancer의 기존 인스턴스를 업데이트 하거나 삭제 합니다.](./update-load-balancer-with-vm-scale-set.md)
