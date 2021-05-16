---
title: Azure HDInsight의 전송 계층 보안
description: TLS(전송 계층 보안) 및 SSL(Secure Sockets Layer)은 컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 57dd7cbfe83b22c0221193af3c6ac2912b40499c
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108278599"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight의 전송 계층 보안

공용 클러스터 엔드포인트 `https://CLUSTERNAME.azurehdinsight.net`을 통한 HDInsight 클러스터 연결은 클러스터 게이트웨이 노드를 통해 프록시됩니다. 이러한 연결은 TLS라는 프로토콜을 사용하여 보호됩니다. 게이트웨이에서 더 높은 버전의 TLS를 적용하면 이러한 연결의 보안이 향상됩니다. 최신 버전의 TLS를 사용해야 하는 이유에 대한 자세한 내용은 [TLS 1.0 문제 해결](/security/solving-tls1-problem)을 참조하세요.

기본적으로 Azure HDInsight 클러스터는 공용 HTTPS 엔드포인트에서 TLS 1.2 연결을 허용하고 이전 버전과의 호환성을 위해 이전 버전을 허용합니다. Azure Portal 또는 Resource Manager 템플릿을 사용하여 클러스터를 만드는 동안 게이트웨이 노드에서 지원되는 최소 TLS 버전을 제어할 수 있습니다. 포털의 경우 클러스터를 만드는 동안 **보안 + 네트워킹** 탭에서 TLS 버전을 선택합니다. 배포 시 Resource Manager 템플릿에 **minSupportedTlsVersion** 속성을 사용합니다. 샘플 템플릿은 [HDInsight 최소 TLS 1.2 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-minimum-tls/azuredeploy.json)을 참조하세요. 이 속성은 "1.0", "1.1" 및 "1.2"라는 세 가지 값을 지원하며, 이는 각각 TLS 1.0+, TLS 1.1+ 및 TLS 1.2+에 해당합니다.


## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에 대한 가상 네트워크 계획](./hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight 클러스터에 가상 네트워크 만들기](hdinsight-create-virtual-network.md)
* [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md).