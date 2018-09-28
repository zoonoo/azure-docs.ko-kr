---
title: Azure 데이터 탐색기 클러스터의 상태 확인
description: 이 문서에서는 Azure 데이터 탐색기 클러스터가 정상 상태인지를 확인하는 단계를 설명합니다.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0746247d2c912ba66e81b95f45b168e32b522130
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988430"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터의 상태 확인

Azure 데이터 탐색기 클러스터의 상태에 영향을 주는 요인으로는 CPU, 메모리, 디스크 하위 시스템 등 여러 가지가 있습니다. 이 문서에서는 클러스터의 상태를 측정하기 위해 수행할 수 있는 몇 가지 기본적인 단계를 설명합니다.

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com)에 로그인합니다.

1. 왼쪽 창에서 클러스터를 선택하고 다음 명령을 실행합니다.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    출력이 1이면 클러스터가 정상인 것이고 0이면 비정상인 것입니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하여 클러스터로 이동합니다.

1. 다음 그림과 같이 **모니터링**에서 **메트릭**을 선택하고 **연결 유지**를 선택합니다. 출력이 1에 가까우면 클러스터는 정상 상태입니다.

    ![클러스터 연결 유지 메트릭](media/check-cluster-health/portal-metrics.png)

1. CPU 및 메모리 캐싱 등의 다른 트릭을 추가하여 클러스터의 리소스 사용률을 측정합니다.

1. 클러스터 상태 문제 진단과 관련하여 지원이 필요한 경우에는 [Azure Portal](https://portal.azure.com)에서 지원 요청을 개설하세요.