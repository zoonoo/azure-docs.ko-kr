---
title: Azure HDInsight를 통해 클러스터 생성 실패 문제 해결
description: Azure HDInsight에 대한 아파치 클러스터 생성 문제를 해결하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: a95c4d654650276c815c5b23fb2c6f8a6d06bc2e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383462"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Azure HDInsight를 통해 클러스터 생성 실패 문제 해결

다음 문제는 클러스터 생성 실패의 가장 일반적인 근본 원인입니다.

- 권한 문제
- 리소스 정책 제한
- 방화벽
- 리소스 잠금
- 지원되지 않는 구성 요소 버전
- 스토리지 계정 이름 제한 사항
- 서비스 중단

## <a name="permissions-issues"></a>권한 이슈

Azure Data Lake Storage Gen2을 사용하고 있고 ```AmbariClusterCreationFailedErrorCode``` ```Internal server error occurred while processing the request. Please retry the request or contact support.```, Azure 포털을 열고, 저장소 계정으로 이동하고, IAM(액세스 제어)에서 오류가 발생하면 **저장소 Blob 데이터 참여자** 또는 **저장소 Blob 데이터 소유자** 역할이 구독에 대해 사용자에게 **할당된 관리ID에** 대한 액세스 권한을 할당했는지 확인합니다. 자세한 지침은 [Data Lake Storage Gen2 계정에서 관리 ID에 대한 권한 설정](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account)을 참조하세요.

Azure Data Lake 저장소 Gen1을 사용하는 경우 [여기에서](../hdinsight-hadoop-use-data-lake-store.md)설정 및 구성 지침을 참조하십시오. 데이터 레이크 스토리지 Gen1은 HBase 클러스터에서 지원되지 않으며 HDInsight 버전 4.0에서는 지원되지 않습니다.

Azure Storage를 사용하는 경우 클러스터를 만드는 동안 저장소 계정 이름이 유효한지 확인합니다.

## <a name="resource-policy-restrictions"></a>리소스 정책 제한

구독 기반 Azure 정책은 공용 IP 주소 생성을 거부할 수 있습니다. HDInsight 클러스터를 생성하려면 두 개의 공용 IP가 필요합니다.  

일반적으로 다음 정책은 클러스터 생성에 영향을 줄 수 있습니다.

* 구독 내에서 IP 주소 & 로드 밸러버를 생성하지 못하도록 하는 정책입니다.
* 저장소 계정 생성을 방지하는 정책입니다.
* 네트워킹 리소스(IP 주소/로드 밸러저)의 삭제를 방지하는 정책입니다.

## <a name="firewalls"></a>방화벽

가상 네트워크 또는 저장소 계정의 방화벽은 HDInsight 관리 IP 주소와의 통신을 거부할 수 있습니다.

아래 표의 IP 주소에서 트래픽을 허용합니다.

| 원본 IP 주소 | 대상 | Direction |
|---|---|---|
| 168.61.49.99 | *:443 | 인바운드 |
| 23.99.5.239 | *:443 | 인바운드 |
| 168.61.48.131 | *:443 | 인바운드 |
| 138.91.141.162 | *:443 | 인바운드 |

또한 클러스터가 생성되는 지역에 특정IP 주소를 추가합니다. 각 Azure 지역의 주소 목록은 [HDInsight 관리 IP 주소를](../hdinsight-management-ip-addresses.md) 참조하십시오.

고속 경로 또는 사용자 지정 DNS 서버를 사용하는 경우 [여러 네트워크를 연결하는 Azure HDInsight에 대한 가상 네트워크 계획을](../hdinsight-plan-virtual-network-deployment.md#multinet)참조하세요.

## <a name="resources-locks"></a>리소스 잠금  

가상 네트워크 및 리소스 그룹에 잠금이 없는지 [확인합니다.](../../azure-resource-manager/management/lock-resources.md) 리소스 그룹이 잠긴 경우 클러스터를 만들거나 삭제할 수 없습니다. 

## <a name="unsupported-component-versions"></a>지원되지 않는 구성 요소 버전

지원되는 [Azure HDInsight 버전과 솔루션의](../hdinsight-component-versioning.md) [아파치 하두롭 구성 요소를](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) 사용하고 있는지 확인합니다.  

## <a name="storage-account-name-restrictions"></a>스토리지 계정 이름 제한 사항

스토리지 계정 이름은 24자를 초과할 수 없으며 특수 문자가 있으면 안 됩니다. 이러한 제한은 스토리지 계정의 기본 컨테이너 이름에도 적용됩니다.

클러스터 만들기에도 다른 이름 지정 제한이 적용됩니다. 자세한 내용은 [클러스터 이름 제한을](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)참조하십시오.

## <a name="service-outages"></a>서비스 중단

잠재적인 중단 또는 서비스 문제가 있는지 [Azure 상태를](https://status.azure.com) 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Virtual Network를 사용하여 Azure HDInsight 확장](../hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Azure HDInsight 클러스터에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md)
* [Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정](../hdinsight-hadoop-provision-linux-clusters.md)
