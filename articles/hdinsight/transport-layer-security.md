---
title: Azure HDInsight의 전송 계층 보안
description: TLS (전송 계층 보안) 및 SSL (secure sockets layer)은 컴퓨터 네트워크에 대 한 통신 보안을 제공 하는 암호화 프로토콜입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183503"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight의 전송 계층 보안

공용 클러스터 끝점 `https://CLUSTERNAME.azurehdinsight.net` 을 통해 HDInsight 클러스터에 연결 하는 것은 클러스터 게이트웨이 노드를 통해 프록시 됩니다. 이러한 연결은 TLS 라는 프로토콜을 사용 하 여 보호 됩니다. 게이트웨이에서 더 높은 버전의 TLS를 적용 하면 이러한 연결의 보안이 향상 됩니다. 최신 버전의 TLS를 사용 해야 하는 이유에 대 한 자세한 내용은 [tls 1.0 문제 해결](https://docs.microsoft.com/security/solving-tls1-problem)을 참조 하세요.

기본적으로 Azure HDInsight 클러스터는 공용 HTTPS 끝점에서 TLS 1.2 연결을 허용 하 고 이전 버전과의 호환성을 위해 이전 버전을 허용 합니다. Azure Portal 또는 리소스 관리자 템플릿을 사용 하 여 클러스터를 만드는 동안 게이트웨이 노드에서 지원 되는 최소 TLS 버전을 제어할 수 있습니다. 포털의 경우 클러스터를 만드는 동안 **보안 + 네트워킹** 탭에서 TLS 버전을 선택 합니다. 배포 시 리소스 관리자 템플릿의 경우 **Minsupportedtlsversion** 속성을 사용 합니다. 샘플 템플릿은 [HDInsight 최소 TLS 1.2 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)을 참조 하세요. 이 속성은 "1.0", "1.1" 및 "1.2"의 세 가지 값을 지원 합니다 .이는 각각 TLS 1.0 +, TLS 1.1 + 및 TLS 1.2 +에 해당 합니다.

> [!IMPORTANT]
> 2020 년 6 월 30 일부 터 Azure HDInsight는 모든 HTTPS 연결에 TLS 1.2 이상 버전을 적용 합니다. 모든 클라이언트에서 TLS 1.2 이상 버전을 처리할 수 있도록 준비 하는 것이 좋습니다. 자세한 내용은 [Azure HDINSIGHT TLS 1.2 적용](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에 대 한 가상 네트워크 계획](./hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight 클러스터에 대 한 가상 네트워크를 만듭니다](hdinsight-create-virtual-network.md).
* [네트워크 보안 그룹](../virtual-network/security-overview.md).
