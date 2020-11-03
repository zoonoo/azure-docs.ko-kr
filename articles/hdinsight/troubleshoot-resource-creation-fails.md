---
title: Azure HDInsight에서 리소스 만들기 오류 문제 해결
description: 이 문서에서는 일반적인 용량 문제 오류와 완화 기법을 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 2e9f6898dba559f557f49cc0ee9ff39644da243f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287356"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Azure HDInsight에서 리소스 만들기 오류 문제 해결

이 문서에서는 Azure HDInsight와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>오류: 배포가 ' 800 '의 할당량을 초과 합니다.

Azure의 할당량 한도는 리소스 그룹당 800개 배포입니다. 리소스 그룹, 구독, 계정 또는 기타 범위 마다 다른 할당량이 적용 됩니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용 된 것 보다 더 많은 코어를 사용 하 여 가상 컴퓨터를 배포 하려는 경우 할당량을 초과 했다는 오류 메시지가 표시 됩니다.

이 문제를 해결 하려면 Azure Portal, CLI 또는 PowerShell을 사용 하 여 더 이상 필요 하지 않은 배포를 삭제 합니다.

자세한 내용은 [리소스 할당량에 대한 오류 해결](../azure-resource-manager/templates/error-resource-quota.md)을 참조하세요.

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>오류: 최대 노드가이 영역에서 사용 가능한 코어 수를 초과 했습니다.

지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용 된 것 보다 더 많은 코어를 사용 하 여 리소스를 배포 하려는 경우 할당량을 초과 했다는 오류 메시지가 표시 됩니다.

할당량 증가를 요청하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **도움말 + 지원** 을 선택 합니다.

1. **새 지원 요청** 을 선택합니다.

1. **새 지원 요청** 페이지의 **기본 사항** 탭에서 다음 정보를 제공 합니다.

   * **문제 유형:** **서비스 및 구독 제한 (할당량)** 을 선택 합니다.
   * **구독:** 수정 하려는 구독을 선택 합니다.
   * **할당량 유형:** **HDInsight** 를 선택 합니다.

자세한 내용은 [코어를 늘리기 위한 지원 티켓 만들기](hdinsight-capacity-planning.md#quotas)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]