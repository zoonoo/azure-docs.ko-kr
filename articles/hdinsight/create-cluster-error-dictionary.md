---
title: 클러스터 오류 사전 만들기
description: 클러스터 오류 사전을 만드는 방법을 알아봅니다.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483064"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: 클러스터 생성 오류

이 문서에서는 클러스터를 만들 때 발생 하는 오류에 대 한 해결 방법을 설명 합니다. 

> [!NOTE]
> 아래 목록에 있는 처음 세 개의 오류는 HDInsight 제품에서 CsmDocument_2_0 클래스를 사용 하는 경우 유효성 검사 오류로 인해 발생 합니다.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>오류 코드: DeploymentDocument ' CsmDocument_2_0 '이 (가) 유효성 검사에 실패 했습니다.

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**오류**: 스크립트 동작 위치에 액세스할 수 없습니다. URI:\<스크립트 동작 URL\>

**오류 메시지: "원격 서버에서 오류를 반환 했습니다. (404) 찾을 수 없음"**

### <a name="cause"></a>원인
클러스터 만들기 요청의 일부로 제공 되는 스크립트 작업 URL은 HDInsight 서비스에서 액세스할 수 없습니다. 스크립트 작업에 액세스 하려고 하면 "ErrorMessage"가 표시 됩니다.

### <a name="resolution"></a>해상도 
  - Http/https URL의 경우 incognito 브라우저 창에서 URL을 열려고 시도 하 여 확인할 수 있습니다. 
  - WASB URL의 경우 요청에 지정 된 저장소 계정에 스크립트가 있는지 확인 합니다. 이 저장소 계정에 대 한 저장소 키가 정확한 지 확인 합니다. 
  - ADLS URL의 경우 스크립트가 저장소 계정에 있는지 확인 합니다.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>오류 코드: DeploymentDocument ' CsmDocument_2_0 '이 (가) 유효성 검사에 실패 했습니다.

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**오류**: 스크립트 동작 위치에 액세스할 수 없습니다. URI: \<스크립트 동작 URL\>

**오류 메시지: 지정 된 스크립트 URI \<스크립트 URI\> ADLS에 있지만이 클러스터에 data lake storage 주체가 없습니다.**

### <a name="cause"></a>원인
클러스터 만들기 요청의 일부로 제공 되는 스크립트 작업 URL은 HDInsight 서비스에서 액세스할 수 없습니다. 스크립트 작업에 액세스 하려고 하면 "ErrorMessage"가 표시 됩니다. 

### <a name="resolution"></a>해상도

해당 하는 Azure Data Lake Store Gen 1 계정이 클러스터에 추가 되었는지 확인 합니다. Azure Data Lake Store Gen 1 계정에 액세스 하는 데 사용 되는 서비스 사용자도 클러스터에 추가 됩니다. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>오류 코드: DeploymentDocument ' CsmDocument_2_0 '이 (가) 유효성 검사에 실패 했습니다.

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**오류**: VM 크기 '\<고객 지정 vm 크기\>'이 (가) 요청에 제공 되지 않았거나 '\<role\>' 역할에 대해 지원 되지 않습니다. 유효한 값은 \<역할\>올바른 VM 크기입니다.

### <a name="cause"></a>원인
사용자가 지정한 VM 크기는 역할에 대해 허용 되지 않습니다. 이는 VM 크기 값이 예상 대로 작동 하지 않거나 컴퓨터 역할에 적합 하지 않기 때문에 발생할 수 있습니다. 

### <a name="resolution"></a>해상도
오류 메시지에는 VM 크기에 대 한 유효한 값이 나열 됩니다. 이러한 유효한 값 중 하나를 선택 하 고 클러스터 만들기 요청을 다시 시도 하세요. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>오류 코드: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**오류**: VirtualNetworkId가 잘못 되었습니다. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>원인
클러스터를 만드는 동안 지정 된 **VirtualNetworkId** 값의 형식이 올바르지 않습니다. 

### <a name="resolution"></a>해상도
**VirtualNetworkId** 및 서브넷이 올바른 형식 인지 확인 합니다. **VirtualnetworkId** 값을 얻으려면 Azure Portal로 이동 하 여 가상 네트워크를 선택 하 고 메뉴에서 **속성** 을 선택 합니다. **ResourceID** 속성은 **VirtualNetworkId** 값입니다. 

가상 네트워크 ID의 예는 다음과 같습니다. 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>오류 코드: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**오류**: 사용자 지정 스크립트 작업의 오류로 인해 클러스터를 배포 하지 못했습니다. 실패 한 작업: SCRIPT_NAME\>\<Ambari UI로 이동 하 여 오류를 추가로 디버그 하세요.

### <a name="cause"></a>원인
클러스터를 성공적으로 배포한 후에 클러스터 만들기 요청 중에 제공 된 사용자 지정 스크립트를 실행 합니다. 이 오류 코드는 이름이 \<SCRIPT_NAME\>이 사용자 지정 스크립트를 실행 하는 동안 오류가 발생 했음을 나타냅니다.   

### <a name="resolution"></a>해상도
사용자의 사용자 지정 스크립트 이므로 사용자는 문제를 해결 하 고 필요한 경우 스크립트를 다시 실행 해야 합니다. 스크립트 실패 문제를 해결 하려면/var/lib/ambari-agent/* 폴더의 로그를 검토 하십시오. 또는 Ambari UI에서 작업 페이지를 열고 **run_customscriptionaction** 작업을 선택 하 여 오류 세부 정보를 확인 합니다. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>오류 코드: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**오류**: 데이터베이스 METASTORE_MAJOR_VERSION\> \<의 \<META_STORE_TYPE\> Metastore 스키마 버전 \<DATABASE_NAME\> 클러스터 버전 \<CLUSTER_VERSION과 (와) 호환 되지 않습니다\>

### <a name="cause"></a>원인
사용자 지정 metastore 선택한 HDInsight 클러스터 버전과 호환 되지 않습니다. 현재 HDInsight 4.0 클러스터는 Metastore 버전 3만 지원 합니다. *x*및 HDInsight 3.6은 Metastore 버전 3을 지원 하지 않습니다. *x* 이상. 

### <a name="resolution"></a>해상도
각 HDInsight 클러스터 버전에서 지 원하는 Metastore 버전만 사용 해야 합니다. 사용자 지정 metastore를 지정 하지 않으면 HDInsight는 내부적으로 metastore를 만듭니다. 그러나이 metastore는 클러스터를 삭제할 때 자동으로 삭제 됩니다. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>오류 코드: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**오류**: 크기 조정 작업을 수행 하기 위해 클러스터 관리 끝점에 연결할 수 없습니다. 네트워크 보안 규칙이 클러스터에 대 한 외부 액세스를 차단 하 고 있지 않으며 Ambari (cluster manager) UI에 성공적으로 액세스할 수 있는지 확인 합니다.

### <a name="cause"></a>원인
중요 한 Azure 상태 및 관리 서비스와의 클러스터 통신을 차단 하는 NSG (네트워크 보안 그룹)에 방화벽 규칙이 있습니다. 

### <a name="resolution"></a>해상도
네트워크 **보안 그룹** 를 사용 하 여 네트워크 트래픽을 제어 하려는 경우 HDInsight를 설치 하기 전에 다음 작업을 수행 합니다. 
  - HDInsight에 대해 사용할 Azure 지역을 식별합니다. 
  - HDInsight에 필요한 IP 주소를 식별합니다. 자세한 내용은 [HDInsight 관리 IP 주소](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)를 참조하세요. 
    - HDInsight를 설치 하려는 서브넷의 네트워크 보안 그룹을 만들거나 수정 합니다. 
    - **네트워크 보안 그룹:** IP 주소에서 포트 **443** 의 **인바운드** 트래픽을 허용 합니다. 이렇게 하면 HDInsight management services가 가상 네트워크 외부에서 클러스터에 연결할 수 있습니다. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>오류 코드: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**오류**: 관리 id에 저장소 계정에 대 한 권한이 없습니다. 저장소 계정에 대 한 관리 Id에 ' 저장소 Blob 데이터 소유자 ' 역할이 할당 되어 있는지 확인 하세요. 저장소:/subscriptions/\<구독 ID\>/Wsourcegs/\< 리소스 그룹 이름\>/providers/Microsoft.Storage/storageAccounts/\<저장소 계정 이름\>, 관리 Id:/subscriptions/\<구독 ID\>/Ssourcegs//\< 리소스 그룹 이름\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<사용자 관리 Id 이름\>

### <a name="cause"></a>원인
**Id를 관리** 하기 위해 필요한 권한이 제공 되지 않았습니다. **사용자 할당 관리 id** 에 ADLS Gen2 저장소 계정에 대 한 Blob Storage 참가자 역할이 없습니다. 

### <a name="resolution"></a>해상도

Azure Portal 열고 저장소 계정으로 이동 하 여 **Access Control (IAM)** 에서 찾고 저장소 Blob 데이터 참가자 또는 저장소 Blob 데이터 소유자 역할에 구독에 대 한 **사용자 할당 관리 id** 에 대 한 "" 할당 "액세스 권한이 있는지 확인 합니다. 자세한 내용은 [Data Lake Storage Gen2 계정에서 관리 되는 id에 대 한 사용 권한 설정](hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조 하세요. 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>오류 코드: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**오류**: 네트워크 보안 그룹/Subscriptions/\<SubscriptionID >\/resourcegroups/<Resource Group name> default/providers/\<네트워크 보안 그룹 이름에 대 한 보안 규칙이 서브넷/Subscriptions/\> SubscriptionID\<> resourcegroups/\/리소스 그룹 이름\<RG-westeurope-tomtom-default > provider/\/Virtual 네트워크 이름 >\/서브넷/\<서브넷 이름\> 
는 필요한 인바운드 및/또는 아웃 바운드 연결을 허용 하지 않습니다. 자세한 내용은 [Azure HDInsight에 대 한 가상 네트워크 계획](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)을 참조 하거나 지원 서비스에 문의 하세요. * *

### <a name="cause"></a>원인
NSGs (네트워크 보안 그룹) 또는 UDRs (사용자 정의 경로)를 사용 하 여 HDInsight 클러스터에 대 한 인바운드 트래픽을 제어 하는 경우 클러스터가 중요 한 Azure 상태 및 관리 서비스와 통신할 수 있는지 확인 해야 합니다.

### <a name="resolution"></a>해상도
네트워크 **보안 그룹** 를 사용 하 여 네트워크 트래픽을 제어 하려는 경우 HDInsight를 설치 하기 전에 다음 작업을 수행 합니다. 
  - HDInsight에 사용할 Azure 지역을 확인 하 고, [상태 및 관리 서비스: 특정](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)지역에 대 한 IP 주소의 안전한 목록을 만듭니다.
  - HDInsight에 필요한 IP 주소를 식별 합니다. 자세한 내용은 [HDInsight 관리 IP 주소](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)를 참조 하세요. 
  - HDInsight를 설치 하려는 서브넷의 네트워크 보안 그룹을 만들거나 수정 합니다. **네트워크 보안 그룹**: IP 주소에서 포트 **443** 에 대 한 인바운드 트래픽을 허용 합니다. 이렇게 하면 HDInsight management services가 가상 네트워크 외부에서 클러스터에 연결할 수 있습니다.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>오류 코드: 클러스터 설치에서 하나 이상의 호스트에 구성 요소를 설치 하지 못했습니다.

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**오류**: 클러스터 설치 프로그램에서 하나 이상의 호스트에 구성 요소를 설치 하지 못했습니다. 요청을 다시 시도하세요. 

### <a name="cause"></a>원인 
일반적으로이 오류는 일시적인 문제가 있거나 Azure 중단이 발생 한 경우에 생성 됩니다. 

### <a name="resolution"></a>해상도

클러스터 배포에 영향을 줄 수 있는 잠재적인 Azure 가동 중단에 대 한 [azure 상태](https://status.azure.com/status) 페이지를 확인 합니다. 중단이 없으면 클러스터 배포를 다시 시도 합니다. 

## <a name="next-steps"></a>다음 단계

클러스터를 만들 때 발생 하는 문제 해결에 대 한 자세한 내용은 [Azure HDInsight를 사용 하 여 클러스터 만들기 오류 문제 해결](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)을 참조 하세요.
