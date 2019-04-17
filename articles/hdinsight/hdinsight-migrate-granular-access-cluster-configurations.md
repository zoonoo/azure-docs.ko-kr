---
title: 구성 액세스-Azure HDInsight 클러스터에 대 한 변경 내용
description: 중요 한 클러스터 구성 필드에 액세스 하려면 변경 내용에 알아봅니다.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610244"
---
# <a name="changes-to-cluster-configuration-access"></a>클러스터 구성 액세스 변경

Azure HDInsight SDK의 최신 릴리스는 더 세분화 된 역할 기반 액세스를 지원 하도록 중요 한 정보를 가져올 일부 중요 한 변경 내용을 제공 합니다. 이 중 일부를 변경 되 면 일부 **작업이 필요할 수 있습니다** 영향을 받는 방법 중 하나를 사용 하는 경우.

## <a name="sdk-for-net-500"></a>5.0.0.NET 용 SDK

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 됩니다 **중요 한 매개 변수를 더 이상 반환** 예: 저장소 키 (코어 사이트) 또는 HTTP 자격 증명 (게이트웨이).
    - 모든 구성에 중요 한 매개 변수를 포함 하 여 검색을 사용 하 여 `ConfigurationOperationsExtensions.List` 앞으로 이동 합니다.  참고 '읽기' 역할을 사용 하 여 사용자는이 메서드를 사용할 수 없습니다. 이렇게 하면 세부적인 제어는 사용자가 클러스터에 대 한 중요 한 정보에 액세스할 수 있습니다. 
    - 방금 HTTP 게이트웨이 자격 증명을 검색 하려면 `ClusterOperationsExtensions.GetGatewaySettings`합니다. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 이제 사용 되지 않으며 바뀌었습니다 `ClusterOperationsExtensions.UpdateGatewaySettings`합니다. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 및 [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) 이제 사용 되지 않습니다. HTTP 이제 항상 설정 되어 있으므로 이러한 메서드는 더 이상 필요 합니다.