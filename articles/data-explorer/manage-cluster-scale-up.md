---
title: 변화하는 요구를 수용하기 위해 Azure 데이터 탐색기 클러스터 크기 조정
description: 이 문서에서는 변화하는 요구에 따라 Azure Data Explorer 클러스터를 확장 및 축소하는 단계를 설명합니다.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415337"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>변화하는 요구를 수용하도록 클러스터 확장 관리

클러스터 크기를 적절하게 조정하는 것은 Azure 데이터 탐색기의 성능에 중요합니다. 하지만 100% 정확도로 클러스터에 대한 요구를 예측할 수 없습니다. 정적 클러스터 크기는 사용량 미달 또는 사용량 초과로 이어질 수 있으며, 둘 다 이상적인 경우는 아닙니다. 더 나은 방법은 변화하는 요구에 따라 용량 및 CPU를 추가하고 제거하여 클러스터의 *크기를 조정*하는 것입니다. 크기 조정, 확장 및 축소를 위한 두 가지 워크플로가 있습니다. 이 문서에서는 클러스터 확장을 관리하는 방법을 보여 줍니다.

1. 클러스터로 이동하고 **설정** 아래에서 **강화**를 선택합니다.

    사용 가능한 SKU 목록이 제공됩니다. 예를 들어 아래 그림에는 사용 가능한 SKU가 D14_V2 하나뿐입니다.

    ![강화](media/manage-cluster-scale-up/scale-up.png)

    D13_V2는 클러스터의 현재 SKU이므로 사용할 수 없습니다. L8 및 L16은 클러스터가 있는 지역에서 사용할 수 없으므로 비활성화됩니다.

1. SKU를 변경하려면 원하는 SKU를 선택하고 **선택** 단추를 누릅니다.

> [!NOTE]
> 강화 프로세스는 몇 분 정도 걸릴 수 있으며, 그 시간 동안 클러스터는 일시 중단됩니다. 크기를 축소하면 클러스터 성능이 손상될 수 있습니다.

이제 Azure Data Explorer 클러스터에 대해 확장 또는 축소 작업을 수행했습니다. 자동 크기 조정이라고도 하는 [클러스터 확장](manage-cluster-scale-out.md)을 수행하여 지정한 메트릭에 따라 동적으로 크기를 조정할 수도 있습니다.

클러스터 크기 조정 문제와 관련하여 지원이 필요한 경우에는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 지원 요청을 개설하세요.
