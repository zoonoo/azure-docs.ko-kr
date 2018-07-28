---
title: Azure Stack에서 할당량 유형은 | Microsoft Docs
description: Azure Stack에서 서비스 및 리소스에 대 한 사용 가능한 다른 할당량 유형을 검토 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/27/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: 172c32c1f3796ec95336543d7d0ea149e63cfb22
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331142"
---
# <a name="quota-types-in-azure-stack"></a>Azure Stack에서 할당량 유형

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

[할당량](azure-stack-plan-offer-quota-overview.md#plans) 사용자 구독 프로 비전 하거나 사용할 수 있는 리소스의 한계를 정의 합니다. 예를 들어 할당량 사용자 최대 5 개의 Vm을 만들 수 있습니다. 각 리소스에는 자체 할당량 유형을 가질 수 있습니다.

## <a name="compute-quota-types"></a>계산 할당량 형식

| **형식** | **기본값** | **설명** |
| --- | --- | --- |
| 가상 컴퓨터의 최대 수 | 20 | 이 위치에서 구독을 만들 수 있는 가상 머신의 최대 수입니다. |
| 가상 컴퓨터 코어의 최대 수 | 50 | 이 위치에 구독을 만들 수 있는 코어의 최대 수 (예를 들어는 A3 VM에 4 개 코어). |
| 가용성 집합의 최대 | 10 | 이 위치에 만든 가용성 집합의 최대 수입니다. |
| 가상 머신 확장의 최대 수 설정 | 20 | 이 위치에 만들 수 있는 가상 머신 확장 집합의 최대 수입니다. |

## <a name="storage-quota-types"></a>저장소 할당량 유형

| **항목** | **기본값** | **설명** |
| --- | --- | --- |
| 최대 용량 (GB) |500 |이 위치에 구독에서 사용할 수 있는 총 저장소 용량입니다. |
| 저장소 계정의 총 수 |20 |이 위치에서 구독을 만들 수 있는 저장소 계정의 최대 수입니다. |

> [!NOTE]  
> 저장소 할당량을 적용 하기 전에 최대 2 시간이 걸릴 수 있습니다.

## <a name="network-quota-types"></a>네트워크 할당량 유형

| **항목** | **기본값** | **설명** |
| --- | --- | --- |
| 최대 공용 Ip |50 |이 위치에서 구독을 만들 수 있는 공용 Ip의 최대 수입니다. |
| 최대 가상 네트워크 |50 |이 위치에서 구독을 만들 수 있는 가상 네트워크의 최대 수입니다. |
| 최대 가상 네트워크 게이트웨이 |1 |이 위치에서 구독을 만들 수 있는 가상 네트워크 게이트웨이 (VPN Gateway)의 최대 수입니다. |
| 최대 네트워크 연결 |2 |이 위치에서 모든 가상 네트워크 게이트웨이 간에 구독을 만들 수 있는 네트워크 연결 (지점 간 또는 사이트 간)의 최대 수입니다. |
| 최대 부하 분산 장치 |50 |이 위치에서 구독을 만들 수 있는 부하 분산 장치의 최대 수입니다. |
| 최대 NIC 수 |100 |이 위치에서 구독을 만들 수 있는 네트워크 인터페이스의 최대 수입니다. |
| 최대 네트워크 보안 그룹 |50 |이 위치에서 구독을 만들 수 있는 네트워크 보안 그룹의 최대 수입니다. |

## <a name="view-an-existing-quota"></a>기존 할당량을 보려면

1. 관리 포털의 기본 대시보드에서 찾을 합니다 **리소스 공급자** 바둑판식으로 배열 합니다.
2. 보려는 같은 원하는 할당량을 사용 하 여 서비스를 선택 **계산** 하거나 **저장소**합니다.
3. 선택 **할당량**, 한 다음 확인 하려는 할당량을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [자세한에 대 한 계획, 제품 및 할당량에 알아봅니다.](azure-stack-plan-offer-quota-overview.md)
- [계획을 만드는 동안 할당량을 만듭니다.](azure-stack-create-plan.md)
