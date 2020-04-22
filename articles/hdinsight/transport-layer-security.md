---
title: Azure HDInsight의 전송 계층 보안
description: 전송 계층 보안(TLS) 및 보안 소켓 계층(SSL)은 컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771964"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight의 전송 계층 보안

공용 클러스터 엔드포인트를 `https://CLUSTERNAME.azurehdinsight.net` 통해 HDInsight 클러스터에 대한 연결은 클러스터 게이트웨이 노드를 통해 프록시됩니다. 이러한 연결은 TLS라는 프로토콜을 사용하여 보호됩니다. 게이트웨이에서 더 높은 버전의 TLS를 적용하여 이러한 연결에 대한 보안을 향상시킵니다. 최신 버전의 TLS를 사용해야 하는 이유에 대한 자세한 내용은 [TLS 1.0 문제 해결을](https://docs.microsoft.com/security/solving-tls1-problem)참조하십시오.

기본적으로 Azure HDInsight 클러스터는 공용 HTTPS 끝점에서 TLS 1.2 연결을 허용하고 이전 버전에서는 이전 버전과 이전 버전의 호환성을 허용합니다. Azure 포털 또는 리소스 관리자 템플릿을 사용하여 클러스터 를 만드는 동안 게이트웨이 노드에서 지원되는 최소 TLS 버전을 제어할 수 있습니다. 포털의 경우 클러스터 를 만드는 동안 **보안 + 네트워킹** 탭에서 TLS 버전을 선택합니다. 배포 시 리소스 관리자 템플릿의 경우 **minSupportedTlsVersion** 속성을 사용합니다. 샘플 템플릿은 [HDInsight 최소 TLS 1.2 빠른 시작 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)참조하십시오. 이 속성은 각각 TLS 1.0+, TLS 1.1+ 및 TLS 1.2+에 해당하는 "1.0", "1.1" 및 "1.2"의 세 가지 값을 지원합니다.

> [!IMPORTANT]
> 2020년 6월 30일부터 Azure HDInsight는 모든 HTTPS 연결에 대해 TLS 1.2 이상 버전을 적용합니다. 모든 클라이언트가 TLS 1.2 이상 버전을 처리할 준비가 되었는지 확인하는 것이 좋습니다. 자세한 내용은 [Azure HDInsight TLS 1.2 적용을](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight를 위한 가상 네트워크 계획](./hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight 클러스터에 대한 가상 네트워크 만들기.](hdinsight-create-virtual-network.md)
* [네트워크 보안 그룹](../virtual-network/security-overview.md).
