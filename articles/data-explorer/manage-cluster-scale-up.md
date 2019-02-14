---
title: 변화하는 요구를 수용하기 위해 Azure 데이터 탐색기 클러스터 크기 조정
description: 이 문서에서는 변화하는 요구 사항에 따라 Azure 데이터 탐색기 클러스터에서 규모를 확장하고 감축하는 단계를 설명합니다.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735316"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>변화하는 요구를 수용하도록 클러스터 강화 관리

클러스터 크기를 적절하게 조정하는 것은 Azure 데이터 탐색기의 성능에 중요합니다. 하지만 100% 정확도로 클러스터에 대한 요구를 예측할 수 없습니다. 정적 클러스터 크기는 사용량 미달 또는 사용량 초과로 이어질 수 있으며, 둘 다 이상적인 경우는 아닙니다. 더 나은 방법은 변화하는 요구에 따라 용량 및 CPU를 추가하고 제거하여 클러스터의 *크기를 조정*하는 것입니다. 이 문서에서는 클러스터 강화를 관리하는 방법을 보여 줍니다.

클러스터로 이동하고 **설정** 아래에서 **강화**를 선택합니다.

그러면 사용 가능한 SKU 목록이 표시됩니다. 활성화된 카드의 목록에서 선택할 수 있습니다. 예를 들어 아래 그림에는 D14_vs에서 선택할 수 있는 SKU가 하나만 있습니다.

![강화](media/manage-cluster-scale-up/scale-up.png)

D13_v2는 클러스터의 현재 SKU이므로 비활성화됩니다. L8 및 L16은 클러스터가 있는 하위 지역에서 사용할 수 없으므로 비활성화됩니다.

SKU를 변경하려면 사용할 SKU만 클릭하고 **선택** 단추를 클릭합니다.

[!NOTE] 강화 프로세스는 몇 분 정도 걸릴 수 있으며, 그 시간 동안 클러스터는 일시 중단됩니다. 크기를 축소하면 클러스터 성능이 손상될 수 있습니다.

클러스터 크기 조정 문제와 관련하여 지원이 필요한 경우에는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 지원 요청을 개설하세요.