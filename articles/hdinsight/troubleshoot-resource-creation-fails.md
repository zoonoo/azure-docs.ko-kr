---
title: Azure HDInsight의 리소스 만들기 실패 문제 해결
description: 이 문서에서는 일반적인 용량 이슈 오류 및 완화 기술이 나와 있습니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: a7594cd9badaaa4e81a28a0877bc8ad97e92cd08
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283900"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Azure HDInsight의 리소스 만들기 실패 문제 해결

이 문서에서는 Azure HDInsight와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>오류: 배포가 '800'의 할당량을 초과합니다.

Azure의 할당량 한도는 리소스 그룹당 800개 배포입니다. 리소스 그룹, 구독, 계정 또는 기타 범위에 따라 다른 할당량이 적용됩니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용되는 것보다 더 많은 코어가 있는 가상 머신을 배포하려고 시도하면 할당량을 초과했다는 오류 메시지가 표시됩니다.

이 문제를 해결하려면 Azure Portal, CLI 또는 PowerShell을 사용하여 더 이상 필요하지 않은 배포를 삭제합니다.

자세한 내용은 [리소스 할당량에 대한 오류 해결](../azure-resource-manager/templates/error-resource-quota.md)을 참조하세요.

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>오류: 최대 노드가 이 영역에서 사용 가능한 코어 수를 초과했습니다.

지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용되는 것보다 더 많은 코어가 있는 리소스를 배포하려고 시도하면 할당량을 초과했다는 오류 메시지가 표시됩니다.

할당량 증가를 요청하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동하고 **도움말 + 지원** 을 선택합니다.

1. **새 지원 요청** 을 선택합니다.

1. **새 지원 요청** 페이지의 **기본 사항** 탭에서 다음 정보를 제공합니다.

   * **문제 유형:** **서비스 및 구독 제한(할당량)** 을 선택합니다.
   * **구독:** 수정하려는 구독을 선택합니다.
   * **할당량 유형:** **HDInsight** 를 선택합니다.

자세한 내용은 [코어를 늘리기 위한 지원 티켓 만들기](hdinsight-capacity-planning.md#quotas)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]