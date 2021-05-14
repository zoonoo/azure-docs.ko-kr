---
title: Azure HDInsight를 사용한 클러스터 만들기 오류 문제 해결
description: Azure HDInsight에 대한 Apache 클러스터 만들기 오류 문제 해결 방법을 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: f909f5714a049f63032c8a23ca1731777a40d332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702864"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Azure HDInsight를 사용한 클러스터 만들기 오류 문제 해결

클러스터 만들기가 실패하는 근본 원인 중 가장 흔한 것으로는 다음과 같은 문제가 있습니다.

- 권한 문제
- 리소스 정책 제한 사항
- 방화벽
- 리소스 잠금
- 지원되지 않는 구성 요소 버전
- 스토리지 계정 이름 제한 사항
- 서비스 중단

## <a name="permissions-issues"></a>권한 이슈

Azure Data Lake Storage Gen2를 사용 중이고`AmbariClusterCreationFailedErrorCode`, “:::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support.":::” 오류를 수신하는 경우 Azure Portal을 열고 스토리지 계정으로 이동한 다음 Access Control(IAM)에서 **Storage Blob 데이터 Contributor** 또는 **Storage Blob 데이터 소유자** 역할에 구독에 대한 **사용자가 할당한 관리 ID** 액세스 권한을 할당하였는지 확인합니다. 자세한 지침은 [Data Lake Storage Gen2에서 관리 ID에 대한 권한 설정](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2)을 참조하세요.

Azure Data Lake Storage Gen1을 사용 중인 경우, [Azure HDInsight 클러스터로 Azure Data Lake Storage Gen1 사용하기](../hdinsight-hadoop-use-data-lake-storage-gen1.md)에 대한 설정 및 구성 지침을 확인하세요. Data Lake Storage Gen1는 HBase 클러스터의 지원을 받지 않으며 HDInsight 버전 4.0에서 지원하지 않습니다.

Azure Storage를 사용하는 경우 클러스터를 만드는 동안 스토리지 계정 이름이 유효하여야 합니다.

## <a name="resource-policy-restrictions"></a>리소스 정책 제한 사항

구독 기반 Azure 정책은 공용 IP 주소 만들기를 거부할 수 있습니다. HDInsight 클러스터를 생성하려면 두 개의 공용 IP가 필요합니다.  

일반적으로 다음 정책은 클러스터 생성에 영향을 줄 수 있습니다.

* 구독 내에서 IP 주소 및 부하 분산 장치를 만들지 못하게 차단하는 정책
* 스토리지 계정을 만들지 못하게 차단하는 정책
* 네트워킹 리소스(IP 주소 /부하 분산 장치) 삭제를 차단하는 정책

## <a name="firewalls"></a>방화벽

가상 네트워크나 스토리지 계정의 방화벽은 HDInsight 관리 IP 주소와의 통신을 거부할 수 있습니다.

다음 테이블의 IP 주소에서 오는 트래픽을 허용합니다.

| 원본 IP 주소 | 대상 | 방향 |
|---|---|---|
| 168.61.49.99 | *:443 | 인바운드 |
| 23.99.5.239 | *:443 | 인바운드 |
| 168.61.48.131 | *:443 | 인바운드 |
| 138.91.141.162 | *:443 | 인바운드 |

또한, 클러스터를 만든 지역으로 지정된 IP 주소를 추가합니다. Azure 지역별 주소 목록은 [HDInsight 관리 IP 주소](../hdinsight-management-ip-addresses.md)를 참조하세요.

Express 경로나 사용자 지정 DNS 서버를 사용하고 있는 경우, [Azure HDInsight에 대한 가상 네트워크 계획 - 다중 네트워크 연결](../hdinsight-plan-virtual-network-deployment.md#multinet)을 참조하세요.

## <a name="resources-locks"></a>리소스 잠금  

[가상 네트워크 및 리소스 그룹 잠금](../../azure-resource-manager/management/lock-resources.md)이 없는지 확인합니다. 리소스 그룹이 잠겨 있는 경우 클러스터를 만들거나 삭제할 수 없습니다. 

## <a name="unsupported-component-versions"></a>지원되지 않는 구성 요소 버전

솔루션에서 [Azure HDInsight 및 Apache Hadoop 구성 요소 지원 버전을](../hdinsight-component-versioning.md) 사용 중인지 확인합니다.  

## <a name="storage-account-name-restrictions"></a>스토리지 계정 이름 제한 사항

스토리지 계정 이름은 24자를 초과할 수 없으며 특수 문자가 있으면 안 됩니다. 이 제한 사항은 스토리지 계정의 기본 컨테이너 이름에도 적용됩니다.

다른 명명 제한 사항도 클러스터 생성에 적용됩니다. 자세한 내용은 [클러스터 명명 제한 사항](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)을 참조하세요.

## <a name="service-outages"></a>서비스 중단

작동 중단 또는 서비스 문제 가능성이 있는 [Azure 상태](https://status.azure.com)를 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Virtual Network를 사용하여 Azure HDInsight 확장](../hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Azure HDInsight 클러스터에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md)
* [Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정](../hdinsight-hadoop-provision-linux-clusters.md)
