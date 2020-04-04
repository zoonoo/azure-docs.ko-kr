---
title: Azure HDInsight 자주 묻는 질문
description: HDInsight에 대한 자주 묻는 질문
keywords: 자주 묻는 질문, FAQ
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a37e1b9bc4a0b953dc727dbab2813dd938ed576
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652226"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: 자주 묻는 질문

이 문서에서는 [Azure HDInsight를](https://azure.microsoft.com/services/hdinsight/)실행하는 방법에 대한 몇 가지 일반적인 질문에 대한 답변을 제공합니다.

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight 클러스터 만들기 또는 삭제

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>HDInsight 클러스터를 프로비전하려면 어떻게 해야 합니까?

사용 가능한 HDInsight 클러스터의 유형과 프로비저닝 방법을 검토하려면 [아파치 하두프, 아파치 스파크, 아파치 카프카 등을 사용하여 HDInsight의 클러스터 설정을](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)참조하십시오.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>기존 HDInsight 클러스터를 삭제하려면 어떻게 해야 합니까?

더 이상 사용되지 않을 때 클러스터를 삭제하는 방법에 대한 자세한 내용은 [HDInsight 클러스터 삭제를](hdinsight-delete-cluster.md)참조하십시오.

만들기 및 삭제 작업 사이에 최소 30~60분 이내로 둡니다. 그렇지 않으면 다음과 같은 오류 메시지와 함께 작업이 실패할 수 있습니다.

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>워크로드에 적합한 코어 또는 노드 수를 선택하려면 어떻게 해야 합니까?

적절한 수의 코어 및 기타 구성 옵션은 다양한 요인에 따라 달라집니다.

자세한 내용은 [HDInsight 클러스터의 용량 계획을](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)참조하십시오.

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>용량 문제로 인해 클러스터 프로비전이 실패할 경우 어떻게 해야 하나요?

이 섹션에서는 일반적인 용량 문제 오류 및 완화 기술을 제공합니다.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>오류: 배포가 '800'의 할당량을 초과합니다.

Azure의 할당량 한도는 리소스 그룹당 800개 배포입니다. 리소스 그룹, 구독, 계정 또는 기타 범위에 따라 다른 할당량이 적용됩니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용된 양보다 더 많은 코어가 있는 가상 컴퓨터를 배포하려고 하면 할당량이 초과되었다는 오류 메시지가 나타납니다.

이 문제를 해결하려면 Azure 포털, CLI 또는 PowerShell을 사용하여 더 이상 필요하지 않은 배포를 삭제합니다.

자세한 내용은 [리소스 할당량에 대한 오류 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)을 참조하세요.

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>오류: 최대 노드가 이 리전의 사용 가능한 코어를 초과했습니다.

지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용된 양보다 더 많은 코어가 있는 리소스를 배포하려고 하면 할당량이 초과되었다는 오류 메시지가 나타납니다.

할당량 증가를 요청하려면 다음 단계를 수행합니다.

1. [Azure 포털로](https://portal.azure.com)이동하여 **도움말 + 지원을**선택합니다.
   
1. **새 지원 요청**을 선택합니다.
   
1. **새 지원 요청** 페이지의 **기본** 탭에서 다음 정보를 제공합니다.
   
   - **문제 유형:** **서비스 및 구독 한도(할당량)를 선택합니다.**
   - **구독:** 수정할 구독을 선택합니다.
   - **할당량 유형:** **HDInsight를**선택합니다.

자세한 내용은 [코어를 늘리기 위한 지원 티켓 만들기](hdinsight-capacity-planning.md#quotas)를 참조하세요.

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight 클러스터의 다양한 유형의 노드는 무엇입니까?

Azure HDInsight 클러스터에는 다양한 유형의 가상 시스템 또는 노드가 있습니다. 각 노드 유형은 시스템 작동에서 중요한 역할을 합니다.

자세한 내용은 [Azure HDInsight 클러스터의 리소스 유형을](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)참조하십시오.

## <a name="individual-components"></a>개별 구성 요소

### <a name="can-i-install-additional-components-on-my-cluster"></a>클러스터에 추가 구성 요소를 설치할 수 있습니까?

예. 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정하려면 다음을 사용하십시오.

- 생성 중 또는 생성 후 스크립트입니다. 스크립트는 Azure 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션인 [스크립트 작업을](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)통해 호출됩니다. 이 구성 옵션은 Azure 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있습니다.

- [HDInsight 응용 프로그램 플랫폼은](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) 생태계 응용 프로그램을 설치합니다.

지원되는 구성 요소 목록은 [HDInsight에서 사용할 수 있는 아파치 하두롭 구성 요소 및 버전은 무엇입니까?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>클러스터에 미리 설치된 개별 구성 요소를 업그레이드할 수 있습니까?

클러스터에 미리 설치된 기본 제공 구성 요소 또는 응용 프로그램을 업그레이드하는 경우 결과 구성은 Microsoft에서 지원되지 않습니다. 이러한 시스템 구성은 Microsoft에서 테스트하지 않았습니다. 이미 업그레이드된 버전의 구성 요소 가 사전 설치된 HDInsight 클러스터의 다른 버전을 사용해 보십시오.

예를 들어 Hive를 개별 구성 요소로 업그레이드하는 것은 지원되지 않습니다. HDInsight는 관리되는 서비스이며, 많은 서비스가 Ambari 서버와 통합되어 테스트됩니다. Hive를 자체적으로 업그레이드하면 다른 구성 요소의 인덱싱된 바이너리가 변경되고 클러스터에 구성 요소 통합 문제가 발생합니다.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>스파크와 카프카가 동일한 HDInsight 클러스터에서 실행될 수 있나요?

아니요, 동일한 HDInsight 클러스터에서 아파치 카프카와 아파치 스파크를 실행할 수 없습니다. 리소스 경합 문제를 방지하려면 Kafka 및 Spark에 대해 별도의 클러스터를 만듭니다.

### <a name="how-do-i-change-timezone-in-ambari"></a>Ambari의 시간대는 어떻게 변경하나요?

1. 클러스터이름이 `https://CLUSTERNAME.azurehdinsight.net`클러스터의 이름인 에서 Ambari 웹 UI를 엽니다.
2. 오른쪽 상단 모서리에서 관리자를 선택 | 설정. 

   ![암바리 설정](media/hdinsight-faq/ambari-settings.png)

3. 사용자 설정 창에서 시간대 드롭다운에서 새 표준 시간대를 선택한 다음 저장을 클릭합니다.

   ![암바리 사용자 설정](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>메타 저장소

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>기존 메타스토어에서 Azure SQL Server로 마이그레이션하려면 어떻게 해야 합니까? 

SQL Server에서 Azure SQL Server로 마이그레이션하려면 [자습서: Sql Server를 DMS를 사용하여 오프라인으로 Azure SQL Database에서 단일 데이터베이스 또는 풀된 데이터베이스로 마이그레이션합니다.](../dms/tutorial-sql-server-to-azure-sql.md)

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>클러스터가 삭제될 때 Hive 메타스토어가 삭제되었습니까?

클러스터가 사용하도록 구성된 메타스토어 유형에 따라 다릅니다.

기본 메타스토어의 경우: 기본 메타스토어는 클러스터 수명 주기의 일부입니다. 클러스터를 삭제하면 해당하는 metastore와 메타데이터도 삭제됩니다.

사용자 지정 메타스토어의 경우: 메타스토어의 수명 주기는 클러스터의 수명 주기에 연결되지 않습니다. 따라서 메타데이터를 잃지 않고 클러스터를 만들고 삭제할 수 있습니다. HDInsight 클러스터를 삭제하고 다시 만든 후에도 Hive 스키마와 같은 메타데이터가 유지됩니다.

자세한 내용은 [Azure HDInsight에서 외부 메타데이터 저장소 사용](hdinsight-use-external-metadata-stores.md)을 참조하세요.

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Hive 메타스토어를 마이그레이션해도 레인저 데이터베이스의 기본 정책도 마이그레이션합니까?

아니요, 정책 정의는 레인저 데이터베이스에 있으므로 레인저 데이터베이스를 마이그레이션하면 정책이 마이그레이션됩니다.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>HIV 메타스토어를 ESP(엔터프라이즈 보안 패키지) 클러스터에서 ESP가 아닌 클러스터로 마이그레이션할 수 있으며 그 반대의 경우도 마찬가지입니까?

예. HIVe 메타스토어를 ESP에서 비 ESP 클러스터로 마이그레이션할 수 있습니다.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hive 메타스토어 데이터베이스의 크기를 어떻게 예측할 수 있습니까?

Hive 메타스토어는 Hive 서버에서 사용하는 데이터 원본에 대한 메타데이터를 저장하는 데 사용됩니다. 크기 요구 사항은 부분적으로 Hive 데이터 원본의 수와 복잡성에 따라 달라지며 미리 예측할 수 없습니다. [Hive 메타스토어 지침에](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)설명된 대로 50DTU 및 250GB의 저장소를 제공하는 S2 계층으로 시작할 수 있으며 병목 현상이 발생하면 데이터베이스를 확장할 수 있습니다.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Azure SQL Database 이외의 다른 데이터베이스를 외부 메타스토어로 지원합니까?

아니요, Microsoft는 Azure SQL 데이터베이스만 외부 사용자 지정 메타스토어로 지원합니다.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>여러 클러스터에서 메타스토어를 공유할 수 있습니까?

예. 동일한 버전의 HDInsight를 사용하는 한 여러 클러스터에서 사용자 지정 메타스토어를 공유할 수 있습니다.

## <a name="connectivity-and-virtual-networks"></a>연결 및 가상 네트워크  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>내 네트워크에서 포트 22 및 23을 차단하면 어떤 의미가 있습니까?

포트 22 및 포트 23을 차단하는 경우 클러스터에 대한 SSH 액세스 권한이 없습니다. 이러한 포트는 HDInsight 서비스에서 사용되지 않습니다.

자세한 내용은 다음 문서를 참조하세요.

- [네트워크 트래픽 제어](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [프라이빗 엔드포인트가 있는 가상 네트워크에서 HDInsight 클러스터로 들어오는 트래픽 보호](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight 관리 IP 주소](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>HDInsight 클러스터와 동일한 서브넷 내에 추가 가상 컴퓨터를 배포할 수 있습니까?

예. HDInsight 클러스터와 동일한 서브넷 내에 추가 가상 컴퓨터를 배포할 수 있습니다. 다음 구성이 가능합니다.

- 에지 노드: [HDInsight의 아파치 하두롭 클러스터에서 빈 에지 노드 사용에](hdinsight-apps-use-edge-node.md)설명된 대로 클러스터에 다른 에지 노드를 추가할 수 있습니다.

- 독립 실행형 노드: 동일한 서브넷에 독립 실행형 가상 컴퓨터를 추가하고 개인 끝점을 `https://<CLUSTERNAME>-int.azurehdinsight.net`사용하여 해당 가상 컴퓨터에서 클러스터에 액세스할 수 있습니다. 자세한 내용은 [네트워크 트래픽 제어를](hdinsight-plan-virtual-network-deployment.md#networktraffic)참조하십시오.

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>에지 노드의 로컬 디스크에 데이터를 저장해야 합니까?

아니요, 로컬 디스크에 데이터를 저장하는 것은 좋은 생각이 아닙니다. 노드가 실패하면 로컬에 저장된 모든 데이터가 손실됩니다. Azure Data Lake Storage Gen2 또는 Azure Blob 저장소에 데이터를 저장하거나 데이터를 저장하기 위해 Azure Files 공유를 탑재하여 데이터를 저장하는 것이 좋습니다.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>기존 HDInsight 클러스터를 다른 가상 네트워크에 추가할 수 있습니까?

너는 할 수 없어요. 가상 네트워크는 프로비저닝 시 지정해야 합니다. 프로비저닝 중에 가상 네트워크를 지정하지 않으면 배포는 외부에서 액세스할 수 없는 내부 네트워크를 만듭니다. 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가를](hdinsight-plan-virtual-network-deployment.md#existingvnet)참조하십시오.

## <a name="security-and-certificates"></a>보안 및 인증서

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에서 맬웨어 보호를 위한 권장 사항은 무엇입니까?

맬웨어 보호에 대한 자세한 내용은 [Azure 클라우드 서비스 및 가상 시스템에 대한 Microsoft 맬웨어 방지](../security/fundamentals/antimalware.md)를 참조하십시오.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>HDInsight ESP 클러스터에 대한 키탭을 만들려면 어떻게 해야 합니까?

도메인 사용자 이름에 대한 Kerberos 키탭을 만듭니다. 나중에 이 키탭을 사용하여 암호를 입력하지 않고 원격 도메인 에 가입한 클러스터를 인증할 수 있습니다. 도메인 이름은 대문자입니다.

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>기존 Azure Active Directory 테넌트를 사용하여 ESP가 있는 HDInsight 클러스터를 만들 수 있습니까?

ESP를 사용하여 HDInsight 클러스터를 만들려면 먼저 Azure Active Directory 도메인 서비스(Azure AD DS)를 사용하도록 설정해야 합니다. 오픈 소스 하두프는 인증을 위해 Kerberos에 의존합니다(OAuth와 는 반대).

VM을 도메인에 가입하려면 도메인 컨트롤러가 있어야 합니다. Azure AD DS는 관리되는 도메인 컨트롤러이며 관리되는 방식으로 안전한 Hadoop 클러스터를 빌드하기 위한 모든 Kerberos 요구 사항을 제공하는 Azure Active Directory의 확장으로 간주됩니다. 관리되는 서비스로서의 HDInsight는 Azure AD DS와 통합되어 종단 간 보안을 제공합니다.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>AAD-DS 보안 LDAP 설정에서 자체 서명된 인증서를 사용하고 ESP 클러스터를 프로비전할 수 있습니까?

인증서 기관에서 발급한 인증서를 사용하는 것이 좋지만 자체 서명된 인증서를 사용하는 것도 ESP에서 지원됩니다. 자세한 내용은 다음을 참조하세요.

- [Azure Active Directory Domain Services 활성화](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [자습서: Azure Active Directory 도메인 관리 도메인에 대해 안전한 LDAP 구성](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>레인저에 표시된 로그인 활동을 끌어오면 어떻게 해야 하나요?

감사 요구 사항의 경우 Azure 모니터 로그에 설명된 대로 Azure Monitor 로그를 사용하여 [HDInsight 클러스터를 모니터링할 것을 권장합니다.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)

### <a name="can-i-disable-clamscan-on-my-cluster"></a>클러스터에서 조개 스캔을 비활성화할 수 있나요?

Clamscan은 HDInsight 클러스터에서 실행되는 바이러스 백신 소프트웨어로 Azure 보안(azsecd)에서 바이러스 공격으로부터 클러스터를 보호하는 데 사용됩니다. 기본 Clamscan 구성을 변경하지 않는 것이 좋습니다.

이 프로세스는 다른 프로세스에서 벗어나거나 주기를 방해하거나 취하지 않습니다. 그것은 항상 다른 프로세스에 굴복합니다. Clamscan의 CPU 스파이크는 시스템이 유휴 상태일 때만 볼 수 있습니다.  

일정을 제어해야 하는 시나리오에서는 다음 단계를 사용할 수 있습니다.

1. 다음 명령을 사용하여 자동 실행을 비활성화합니다.
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. 다음 명령을 루트로 실행하는 Cron 작업을 추가합니다.
   
   `/usr/local/bin/azsecd manual -s clamav`

cron 작업을 설정하고 실행하는 방법에 대한 자세한 내용은 [Cron 작업을 설정하려면 어떻게](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)해야 합니까?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>스파크 ESP 클러스터에서 LLAP를 사용할 수 있는 이유는 무엇입니까?
ESP 스파크 클러스터에서 LLAP는 성능이 아닌 보안상의 이유로 활성화됩니다( 즉, 아파치 레인저). LLAP의 리소스 사용량을 수용하기 위해 더 큰 노드 VM을 사용해야 합니다(예: 최소 D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>ESP 클러스터를 만든 후 AAD 그룹을 추가하면 어떻게 해야 합니까?
이를 달성하는 방법에는 두 가지가 있습니다: 1- 클러스터를 다시 만들고 클러스터를 만들 때 추가 그룹을 추가할 수 있습니다. AAD-DS에서 범위 별 동기화를 사용하는 경우 그룹 B가 범위 동기화에 포함되어 있는지 확인하십시오.
2- ESP 클러스터를 만드는 데 사용된 이전 그룹의 중첩하위 그룹으로 그룹을 추가합니다. 예를 들어 그룹이 `A`있는 ESP 클러스터를 만든 경우 나중에 `B` 그룹을 중첩된 하위 `A` 그룹으로 추가할 수 있으며 약 1시간 후에 자동으로 동기화되어 클러스터에서 사용할 수 있습니다. 

## <a name="storage"></a>스토리지

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>기존 HDInsight 클러스터에 Azure 데이터 레이크 스토리지 Gen2를 추가 저장소 계정으로 추가할 수 있습니까?

아니요, 현재 Blob 저장소가 기본 저장소가 있는 클러스터에 Azure Data Lake Storage Gen2 저장소 계정을 추가할 수 없습니다. 자세한 내용은 [저장소 비교 옵션을](hdinsight-hadoop-compare-storage-options.md)참조하십시오.

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Data Lake 저장소 계정에 대해 현재 연결된 서비스 보안 주체를 찾으려면 어떻게 해야 합니까?

Azure 포털의 클러스터 속성 아래에서 **Data Lake Storage Gen1 액세스에서** 설정을 찾을 수 있습니다. 자세한 내용은 [클러스터 설정 확인](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)을 참조하십시오.
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>HDInsight 클러스터의 저장소 계정 및 Blob 컨테이너 사용량을 계산하는 방법은 무엇입니까?

다음 중 하나를 수행합니다.

- [PowerShell 사용](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- */사용자/하이브/의 크기를 찾습니다. *다음 명령줄을 사용하여 HDInsight 클러스터의 휴지통/폴더:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Blob 저장소 계정에 대한 감사를 설정하면 어떻게 하나요?

Blob 저장소 계정을 감사하려면 [Azure 포털의 저장소 계정 모니터링에서](../storage/common/storage-monitor-storage-account.md)프로시저를 사용하여 모니터링을 구성합니다. HDFS 감사 로그는 로컬 HDFS 파일 시스템에 대해서만 감사 정보를 제공합니다(hdfs://mycluster).  원격 저장소에서 수행되는 작업은 포함되지 않습니다.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Blob 컨테이너와 HDInsight 헤드 노드 간에 파일을 전송하는 방법은 무엇입니까?

헤드 노드에서 다음 셸 스크립트와 유사한 스크립트를 실행합니다.

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> 파일 *filenames.txt는* Blob 컨테이너에 있는 파일의 절대 경로를 갖습니다.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>저장을위한 레인저 플러그인이 있습니까?

현재 Blob 저장소 및 Azure Data Lake 저장소 Gen1 또는 Gen2에 대 한 레인저 플러그인이 없습니다. ESP 클러스터의 경우 HDFS 도구를 사용하여 파일 시스템 수준에서 최소한 세분화된 권한을 수동으로 설정할 수 있으므로 Azure Data Lake Storage를 사용해야 합니다. 또한 Azure Data Lake Storage를 사용하는 경우 ESP 클러스터는 클러스터 수준에서 Azure Active Directory를 사용하여 일부 파일 시스템 액세스 제어를 수행합니다. 

Azure Storage 탐색기를 사용하여 사용자의 보안 그룹에 데이터 액세스 정책을 할당할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Azure AD 사용자가 Hive 또는 다른 서비스를 사용하여 Data Lake Storage Gen2의 데이터를 쿼리할 수 있는 권한을 설정하려면 어떻게 해야 합니까?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Azure Data Lake Storage Gen2와 함께 Azure Storage Explorer를 사용하여 파일 및 디렉터리 수준 사용 권한 설정](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>작업자 노드의 디스크 크기를 늘리지 않고 클러스터의 HDFS 스토리지를 늘릴 수 있습니까?

아니요, 작업자 노드의 디스크 크기를 늘릴 수 없으므로 디스크 크기를 늘리는 유일한 방법은 클러스터를 삭제하고 더 큰 작업자 VM으로 다시 만드는 것입니다. 클러스터를 삭제하면 데이터가 삭제되므로 HDFS를 사용하여 HDInsight 데이터를 저장하지 마십시오. 대신 데이터를 Azure에 저장합니다. 클러스터를 확장하면 HDInsight 클러스터에 추가 용량을 추가할 수도 있습니다.

## <a name="edge-nodes"></a>에지 노드

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>클러스터를 만든 후 에지 노드를 추가할 수 있습니까?

클러스터를 만들 때 HDInsight 클러스터 또는 새 클러스터에 있습니다. 자세한 내용은 [HDInsight의 Apache Hadoop 클러스터에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)을 참조하세요.

### <a name="how-can-i-connect-to-an-edge-node"></a>에지 노드에 연결하려면 어떻게 해야 합니까?

에지 노드를 만든 후 포트 22에서 SSH를 사용하여 에지 노드에 연결할 수 있습니다. 클러스터 포털에서 에지 노드의 이름을 찾을 수 있습니다. 이름은 일반적으로 *-ed로*끝납니다.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>새로 생성된 에지 노드에서 지속스크립트가 자동으로 실행되지 않는 이유는 무엇입니까?

지속스크립트를 사용하여 확장 작업을 통해 클러스터에 추가된 새 작업자 노드를 사용자 지정합니다. 지속스크립트는 에지 노드에 적용되지 않습니다.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>클러스터에서 Tez 쿼리 뷰를 가져오는 REST API 호출은 무엇입니까?

다음 REST 끝점을 사용하여 JSON 형식으로 필요한 정보를 가져올 수 있습니다. 기본 인증 헤더를 사용하여 요청을 합니다.

- Tez 쿼리 보기: *https:\//\<클러스터 이름>.azurehdinsight.net/ws/v1/타임라인/HIVE_QUERY_ID/*
- 테즈 다그 보기: *https:\//\<클러스터 이름>.azurehdinsight.net/ws/v1/타임라인/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Azure Active Directory 사용자를 사용하여 HDI 클러스터에서 구성 세부 정보를 검색하려면 어떻게 해야 합니까?

AAD 사용자와 적절한 인증 토큰을 협상하려면 다음 형식을 사용하여 게이트웨이를 통과하십시오.

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Ambari Restful API를 사용하여 YARN 성능을 모니터링하려면 어떻게 해야 합니까?

동일한 가상 네트워크 또는 피어피 가상 네트워크에서 Curl 명령을 호출하는 경우 명령은 다음과 같습니다.

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
가상 네트워크 외부 또는 피어가 없는 가상 네트워크에서 명령을 호출하는 경우 명령 형식은 다음과 같은 것입니다.

- ESP가 아닌 클러스터의 경우:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- ESP 클러스터의 경우:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl에서 암호를 입력하라는 메시지가 표시됩니다. 클러스터 로그인 사용자 이름에 대해 유효한 암호를 입력해야 합니다.

## <a name="billing"></a>결제

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>HDInsight 클러스터를 배포하는 데 드는 비용은 얼마입니까?

청구와 관련된 가격 및 FAQ에 대한 자세한 내용은 [Azure HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/) 페이지를 참조하십시오.

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight 청구는 언제 시작& 시작됩니까?

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 청구는 분당 비례 배분됩니다.

### <a name="how-do-i-cancel-my-subscription"></a>구독을 취소하려면 어떻게 해야 하나요?

구독을 취소하는 방법에 대한 자세한 내용은 [Azure 구독 취소](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)를 참조하십시오.

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>종량제 구독의 경우 구독을 취소하면 어떻게 되나요?

구독이 취소된 후 구독에 대한 자세한 내용은 [구독을 취소한 후 어떻게 되나요?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>HDInsight 3.6 클러스터를 실행중에도 암바리 UI에서 Hive 버전이 2.1대신 1.2.1000으로 표시되는 이유는 무엇입니까?

Ambari UI에는 1.2개만 나타나지만 HDInsight 3.6에는 하이브 1.2와 하이브 2.1이 모두 포함되어 있습니다.

## <a name="other-faq"></a>기타 자주 묻는 질문

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>HDInsight는 실시간 스트림 처리 기능측면에서 무엇을 제공합니까?

Azure HDInsight에서 스트림 처리의 통합 기능에 대한 자세한 내용은 Azure 의 [스트림 처리 기술 선택을](/azure/architecture/data-guide/technology-choices/stream-processing)참조하십시오.

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>클러스터가 특정 기간 동안 유휴 상태일 때 클러스터의 헤드 노드를 동적으로 종료하는 방법이 있습니까?

HDInsight 클러스터에서는 이 작업을 수행할 수 없습니다. 이러한 시나리오에 Azure 데이터 팩터리를 사용할 수 있습니다.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight는 어떤 규정 준수 제품을 제공합니까?

규정 준수 정보는 [Microsoft 신뢰 센터](https://www.microsoft.com/trust-center) 및 [Microsoft Azure 규정 준수 개요를](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)참조하십시오.
