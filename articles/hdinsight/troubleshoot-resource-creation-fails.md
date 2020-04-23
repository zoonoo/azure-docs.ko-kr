---
title: Azure HDInsight에서 리소스 만들기 오류 문제 해결
description: 이 문서에서는 일반적인 용량 문제 오류와 완화 기법을 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.openlocfilehash: 871e6b1253b0fc9a5b6ae5d3389982cfdd6af3bd
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103179"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Azure HDInsight에서 리소스 만들기 오류 문제 해결

이 문서에서는 Azure HDInsight와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>오류: 배포가 ' 800 '의 할당량을 초과 합니다.

Azure의 할당량 한도는 리소스 그룹당 800개 배포입니다. 리소스 그룹, 구독, 계정 또는 기타 범위 마다 다른 할당량이 적용 됩니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용 된 것 보다 더 많은 코어를 사용 하 여 가상 컴퓨터를 배포 하려는 경우 할당량을 초과 했다는 오류 메시지가 표시 됩니다.

이 문제를 해결 하려면 Azure Portal, CLI 또는 PowerShell을 사용 하 여 더 이상 필요 하지 않은 배포를 삭제 합니다.

자세한 내용은 [리소스 할당량에 대한 오류 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)을 참조하세요.

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>오류: 최대 노드가이 영역에서 사용 가능한 코어 수를 초과 했습니다.

지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용 된 것 보다 더 많은 코어를 사용 하 여 리소스를 배포 하려는 경우 할당량을 초과 했다는 오류 메시지가 표시 됩니다.

할당량 증가를 요청하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **도움말 + 지원**을 선택 합니다.

1. **새 지원 요청**을 선택합니다.

1. **새 지원 요청** 페이지의 **기본 사항** 탭에서 다음 정보를 제공 합니다.

   * **문제 유형:** **서비스 및 구독 제한 (할당량)** 을 선택 합니다.
   * **구독:** 수정 하려는 구독을 선택 합니다.
   * **할당량 유형:** **HDInsight**를 선택 합니다.

자세한 내용은 [코어를 늘리기 위한 지원 티켓 만들기](hdinsight-capacity-planning.md#quotas)를 참조하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
