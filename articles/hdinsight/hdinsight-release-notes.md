---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. 하두프, 스파크, R 서버, 하이브 등에 대한 개발 팁과 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: cdb31f1a46d7f46b69e9e0ad47a77ba6b32a50a0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770827"
---
# <a name="release-notes"></a>릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위한 엔터프라이즈 고객 들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-01092020"></a>릴리스 날짜: 01/09/2020

이 릴리스는 HDInsight 3.6 및 4.0모두에 적용됩니다. HDInsight 릴리스는 며칠 에 걸쳐 모든 지역에서 사용할 수 있습니다. 여기서 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 사항이 표시되지 않으면 며칠 후에 해당 지역에서 릴리스가 라이브로 출시될 때까지 기다려 주십시오.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전 관리 문서](hdinsight-component-versioning.md)를 참조하세요.

## <a name="new-features"></a>새로운 기능
### <a name="tls-12-enforcement"></a>TLS 1.2 적용
TLS(전송 계층 보안) 및 SSL(Secure Sockets Layer)은 컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다. [TLS에](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)대해 자세히 알아보십시오. HDInsight는 공용 HTTP 끝점에서 TLS 1.2를 사용하지만 TLS 1.1은 이전 버전과의 호환성을 위해 계속 지원됩니다. 

이 릴리스를 통해 고객은 공용 클러스터 엔드포인트를 통한 모든 연결에 대해서만 TLS 1.2를 선택할 수 있습니다. 이를 지원하기 위해 새 속성 **minSupportedTlsVersion이** 도입되고 클러스터 생성 중에 지정할 수 있습니다. 속성이 설정되지 않은 경우에도 클러스터는 오늘날의 동작과 동일한 TLS 1.0, 1.1 및 1.2를 계속 지원합니다. 고객은 이 속성의 값을 "1.2"로 설정할 수 있으며, 이는 클러스터가 TLS 1.2 이상만 지원한다는 것을 의미합니다. 자세한 내용은 [전송 계층 보안](./transport-layer-security.md)을 참조하십시오.

### <a name="bring-your-own-key-for-disk-encryption"></a>디스크 암호화를 위해 고유한 키 가져오기
HDInsight의 모든 관리 디스크는 Azure SSE(스토리지 서비스 암호화)로 보호됩니다. 이러한 디스크의 데이터는 기본적으로 Microsoft에서 관리하는 키에 의해 암호화됩니다. 이 릴리스부터 디스크 암호화를 위해 BYOK(사용자 고유의 키)를 가져오고 Azure Key Vault를 사용하여 관리할 수 있습니다. BYOK 암호화는 추가 비용 없이 클러스터를 만드는 동안 한 단계 구성입니다. Azure Key Vault를 사용하여 HDInsight를 관리되는 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가하기만 하면 됩니다. 자세한 내용은 [고객이 관리하는 키 디스크 암호화](https://docs.microsoft.com/azure/hdinsight/disk-encryption)를 참조하십시오.

## <a name="deprecation"></a>사용 중단
이 릴리스에 대한 사용 중단이 없습니다. 예정된 사용 중단에 대한 준비는 [예정된 변경 내용을](#upcoming-changes)참조하십시오.

## <a name="behavior-changes"></a>동작 변경
이 릴리스에 대한 동작이 변경되지 않습니다. 예정된 변경 사항에 대한 준비는 [예정된 변경 내용을](#upcoming-changes)참조하십시오.

## <a name="upcoming-changes"></a>예정된 변경
다음 변경 사항은 향후 릴리스에서 변경됩니다. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>헤드 노드에 최소 4코어 VM이 필요합니다. 
HDInsight 클러스터의 높은 가용성과 안정성을 보장하려면 헤드 노드에 최소 4코어 VM이 필요합니다. 2020년 4월 6일부터 고객은 새로운 HDInsight 클러스터의 헤드 노드로 4코어 이상만 선택할 수 있습니다. 기존 클러스터는 예상대로 계속 실행됩니다. 

### <a name="esp-spark-cluster-node-size-change"></a>ESP 스파크 클러스터 노드 크기 변경 
향후 릴리스에서는 ESP Spark 클러스터의 허용된 최소 노드 크기가 Standard_D13_V2 변경됩니다. A 시리즈 VM은 상대적으로 낮은 CPU 및 메모리 용량으로 인해 ESP 클러스터 문제를 일으킬 수 있습니다. A 시리즈 VM은 새 ESP 클러스터를 만들기 위해 더 이상 사용되지 않습니다.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 가상 시스템 규모 집합으로 이동
HDInsight는 이제 Azure 가상 컴퓨터를 사용하여 클러스터를 프로비전합니다. 향후 릴리스에서 HDInsight는 대신 Azure 가상 시스템 크기 집합을 사용합니다. Azure 가상 시스템 규모 집합에 대해 자세히 알아봅각

### <a name="hbase-20-to-21"></a>HBase 2.0 ~ 2.1
곧 HDInsight 4.0 릴리스에서 HBase 버전은 버전 2.0에서 2.1로 업그레이드됩니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 클러스터 안정성과 성능 향상을 지속적으로 개선하고 있습니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스의 구성 요소 버전 변경사항은 없습니다. HDInsight 4.0 광고 HDInsight 3.6의 현재 구성 요소 버전은 여기에서 확인할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

2020년 3월 18일부터 서유럽 또는 북유럽의 일부 Azure HDInsight 고객은 이러한 리전에서 HDInsight 클러스터를 만들거나 확장할 때 오류 알림을 받았습니다. 이 문제와 관련된 오류는 다음과 같습니다.

- 요청을 처리하는 동안 내부 서버 오류가 발생했습니다. 요청을 다시 시도하거나 지원팀에 문의하십시오.
- 하나 이상의 리소스 배포 작업이 실패했습니다. 자세한 내용은 배포 작업을 나열하십시오. 사용 https://aka.ms/DeployOperations 에 대한 자세한 내용은 봐 주세요
- 사용자 SubscriptionId\<'\>구독 ID ' 리소스 '클러스터\<이름>'을 만들 남아 있는 코어가 없습니다. 필수: \<\>X , 사용 가능: 0.

엔지니어는 이 문제를 인식하고 적극적으로 조사하고 있습니다.

추가 도움말을 보려면 [지원 요청을](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)만듭니다.
