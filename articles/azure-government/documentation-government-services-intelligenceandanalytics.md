---
title: "Azure Government 인텔리전스 + 분석 | Microsoft Docs"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: Azure-Government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 0302a44bb42200c07a313bf2862479c1722af8c4
ms.openlocfilehash: 2a8769f7a611fb22e39db166d3416728f57cbbf4


---
# <a name="azure-government-intelligence--analytics"></a>Azure Government 인텔리전스 + 분석
이 문서에서는 Azure Government 환경의 인텔리전스 및 분석 서비스, 서비스 변형과 고려 사항을 개략적으로 설명합니다.

## <a name="azure-hdinsight"></a>Azure HDInsight
HDInsight는 일반적으로 Azure Government에서 사용할 수 있습니다.

### <a name="variations"></a>변형
다음 HDInsight 기능은 현재 Azure Government에서 사용할 수 없습니다.

* HDInsight는 Windows에서 사용할 수 없습니다.
* Azure Data Lake Store는 Azure Government에서 현재 사용할 수 없습니다. Azure Blob Storage가 현재 사용할 수 있는 유일한 저장소 옵션입니다.
* Azure Active Directory Domain Services 통합은 아직 사용할 수 없습니다. Active Directory Domain Services 없이 보안 Hadoop 클러스터를 만들려면 다음 설정 시나리오 중 하나를 선택합니다.

1. AZURE IAAS에서 실행되는 ACTIVE DIRECTORY와 통합된 HDINSIGHT

  HDInsight를 active directory와 통합하기 위한 가장 간단한 아키텍처입니다. 아래에 아키텍처 다이어그램이 제공됩니다. 이 아키텍처에서는 Azure의 VM 하나 이상에서 실행 중인 active directory 도메인 컨트롤러를 포함하게 됩니다. 일반적으로 이러한 VM은 가상 네트워크 내에 있게 됩니다. HDInsight 클러스터를 배치할 수 있는 새로운 가상 네트워크를 설정할 수 있습니다. active directory에 대한 시야를 포함하는 HDInsight의 경우 [VNET 간 피어링](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal)을 사용하여 이러한 가상 네트워크를 피어링해야 합니다. 
  
  active directory에 설치해야 하는 필수 구성 요소
     * HDInsight 클러스터 VM 및 클러스터에서 사용하는 서비스 주체를 배치할 OU(조직 구성 단위)를 만들어야 합니다. 
     * active directory와 통신하기 위해 LDAPS를 설치해야 합니다. LDAPS를 설치하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 실제 인증서여야 합니다.
     * HDI 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다.
     * 사용할 서비스 계정 또는 사용자 계정이 필요하며 HDInsight 클러스터를 만드는 데 사용됩니다. 이 계정에는 다음 사용 권한이 있어야 합니다.
        * 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
        * 역방향 DNS 프록시 규칙을 만들 수 있는 권한
        * 컴퓨터를 active directory 도메인에 조인할 수 있는 권한
        
2. VPN 설정을 통해 온-프레미스 ACTIVE DIRECTORY와 통합된 HDINSIGHT
  
  이 아키텍처는 아키텍처 #1과 같습니다. 이 경우 유일한 차이점은 active directory가 온-프레미스에 있고 active directory에 대한 HDInsight의 시야가 [Azure에서 온-프레미스 네트워크로 VPN 연결](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction)을 통한다는 점입니다. 이 설정에 대한 아키텍처 다이어그램은 아래와 같습니다. 
  
  온-프레미스 active directory에 설치해야 하는 필수 구성 요소
     * HDInsight 클러스터 VM 및 클러스터에서 사용하는 서비스 주체를 배치할 OU(조직 구성 단위)를 만들어야 합니다.
     * active directory와 통신하기 위해 LDAPS를 설치해야 합니다. LDAPS를 설치하는 데 사용된 인증서는 자체 서명된 인증서가 아닌 실제 인증서여야 합니다.
     * HDI 서브넷의 IP 주소 범위에 대한 도메인에 역방향 DNS 영역을 만들어야 합니다.
     * 사용할 서비스 계정 또는 사용자 계정이 필요하며 HDInsight 클러스터를 만드는 데 사용됩니다. 이 계정에는 다음 사용 권한이 있어야 합니다.
        * 조직 구성 단위 내에 서비스 주체 개체 및 컴퓨터 개체를 만들 수 있는 권한
        * 역방향 DNS 프록시 규칙을 만들 수 있는 권한
        * 컴퓨터를 active directory 도메인에 조인할 수 있는 권한


Azure Government에서 Log Analytics의 URL은 서로 다릅니다.

| 서비스 유형 | Azure 공용 | Azure Government |
| --- | --- | --- |
| HDInsight 클러스터 | \*.azurehdinsight.net | \*.azurehdinsight.us |

자세한 내용은 [Azure HDInsight 공개 문서](../hdinsight/hdinsight-hadoop-introduction.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
부가 정보 및 업데이트를 보려면 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.



<!--HONumber=Jan17_HO2-->


