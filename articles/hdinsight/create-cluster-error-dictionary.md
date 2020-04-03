---
title: Azure HDInsight 클러스터 만들기 - 오류 사전
description: Azure HDInsight 클러스터를 만들 때 발생하는 오류 문제를 해결하는 방법 알아보기
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 803783eddfbffd5c3dbab7353ee00dd7f11a09e5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618895"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: 클러스터 생성 오류

이 문서에서는 클러스터를 만들 때 발생할 수 있는 오류에 대한 해결 방법을 설명합니다.

> [!NOTE]
> 이 문서에서 설명하는 처음 세 가지 오류는 유효성 검사 오류입니다. Azure HDInsight 제품이 **CsmDocument_2_0** 클래스를 사용하는 경우에 발생할 수 있습니다.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>오류 코드: 배포 문서 'CsmDocument_2_0' 유효성 검사에 실패 했습니다.

### <a name="error"></a>Error

"스크립트 작업 위치에 액세스할 수\<없습니다\>URI: 스크립트 작업 URL"

#### <a name="error-message"></a>오류 메시지

"원격 서버에서 (404) 찾을 수 없는 오류가 반환되었습니다."

### <a name="cause"></a>원인

HDInsight 서비스는 클러스터 만들기 요청의 일부로 제공한 스크립트 작업 URL에 액세스할 수 없습니다. 서비스는 스크립트 작업에 액세스하려고 할 때 이전 오류 메시지를 받습니다.

### <a name="resolution"></a>해결 방법

- HTTP 또는 HTTPS URL의 경우 시크릿 브라우저 창에서 URL로 이동하여 URL을 확인합니다.
- WASB URL의 경우 요청에 제공하는 저장소 계정에 스크립트가 있는지 확인합니다. 또한 이 저장소 계정의 저장소 키가 올바른지 확인합니다.
- ADLS URL의 경우 스크립트가 저장소 계정에 있는지 확인합니다.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>오류 코드: 배포 문서 'CsmDocument_2_0' 유효성 검사에 실패 했습니다.

### <a name="error"></a>Error

"스크립트 작업 위치에 액세스할 수 \<\>없습니다 URI: SCRIPT_ACTION_URL"

#### <a name="error-message"></a>오류 메시지

"지정된 스크립트 \<URI\> SCRIPT_URI ADLS에 있지만 이 클러스터에는 데이터 레이크 저장소 주체가 없습니다."

### <a name="cause"></a>원인

HDInsight 서비스는 클러스터 만들기 요청의 일부로 제공한 스크립트 작업 URL에 액세스할 수 없습니다. 서비스는 스크립트 작업에 액세스하려고 할 때 이전 오류 메시지를 받습니다.

### <a name="resolution"></a>해결 방법

해당 Azure 데이터 레이크 저장소 Gen 1 계정을 클러스터에 추가합니다. 또한 데이터 레이크 스토리지 Gen 1 계정에 액세스하는 서비스 주체를 클러스터에 추가합니다.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>오류 코드: 배포 문서 'CsmDocument_2_0' 유효성 검사에 실패 했습니다.

### <a name="error"></a>Error

" VM\<크기\>' CUSTOMER_SPECIFIED_VM_SIZE ' 요청에 제공 되지 않습니다\<\>또는 역할에 대 한 지원 되지 않습니다 ' 역할 ' 역할 '. 유효한 값은 \<\>다음과 같습니다VALID_VM_SIZE_FOR_ROLE .

### <a name="cause"></a>원인

지정한 가상 컴퓨터 크기는 역할에 허용되지 않습니다. VM 크기 값이 예상대로 작동하지 않거나 컴퓨터 역할에 적합하지 않기 때문에 이 오류가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

오류 메시지에는 VM 크기에 대한 유효한 값이 나열됩니다. 이러한 값 중 하나를 선택하고 클러스터 만들기 요청을 다시 시도합니다.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>오류 코드: 잘못된 가상 네트워크 Id  

### <a name="error"></a>Error

"가상네트워크Id가 잘못되었습니다. 가상 네트워크\<ID\>' USER_VIRTUALNETWORKID '*"

### <a name="cause"></a>원인

클러스터 를 만드는 동안 지정한 **VirtualNetworkId** 값이 올바른 형식이 아닙니다.

### <a name="resolution"></a>해결 방법

**VirtualNetworkId** 및 서브넷 값이 올바른 형식인지 확인합니다. **가상네트워크Id** 값을 얻으려면 다음을 수행하십시오.

1. Azure Portal로 이동합니다.
1. 가상 네트워크를 선택합니다.
1. **속성** 메뉴 항목을 선택합니다. **ResourceID** 속성 값은 **가상NetworkId** 값입니다.

가상 네트워크 ID의 예는 다음과 같습니다.

"/구독/c15fd9b8-e2b8-1d4e-aa85-2e66804023b/리소스 그룹/myresourcegroup/공급자/Microsoft.Network/가상 네트워크/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>오류 코드: 사용자 지정 실패 오류 코드

### <a name="error"></a>Error

"사용자 지정 스크립트 작업의 오류로 인해 클러스터 배포가 실패했습니다. 실패한 \<작업:\>SCRIPT_NAME, Ambari UI로 이동하여 오류를 디버깅하십시오."

### <a name="cause"></a>원인

클러스터 만들기 요청 중에 제공한 사용자 지정 스크립트는 클러스터를 성공적으로 배포한 후 실행됩니다. 이 오류 코드는 SCRIPT_NAME \<\>라는 사용자 지정 스크립트를 실행 하는 동안 오류가 발생 했음을 나타냅니다.

### <a name="resolution"></a>해결 방법

스크립트는 사용자 지정 스크립트이므로 문제를 해결하고 필요한 경우 스크립트를 다시 실행하는 것이 좋습니다. 스크립트 오류 문제를 해결하려면 /var/lib/ambari 에이전트/* 폴더의 로그를 검사합니다. 또는 Ambari UI에서 **작업** 페이지를 연 다음 **run_customscriptaction** 작업을 선택하여 오류 세부 정보를 봅니다.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>오류 코드: 잘못된 문서오류 코드

### <a name="error"></a>Error

"데이터베이스 \< \<DATABASE_NAME\> \<METASTORE_MAJOR_VERSION META_STORE_TYPE\>\> Metastore 스키마 버전은 \<\> 클러스터 버전 CLUSTER_VERSION 호환되지 않습니다."

### <a name="cause"></a>원인

사용자 지정 메타스토어가 선택한 HDInsight 클러스터 버전과 호환되지 않습니다. 현재 HDInsight 4.0 클러스터는 메타스토어 버전 3.0 이상만 지원하지만 HDInsight 3.6 클러스터는 메타스토어 버전 3.0 이상만 지원하지 않습니다.

### <a name="resolution"></a>해결 방법

HDInsight 클러스터 버전에서 지원하는 메타스토어 버전만 사용합니다. 사용자 지정 메타스토어를 지정하지 않으면 HDInsight내부적으로 메타스토어를 만들고 클러스터 삭제 시 삭제합니다.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>오류 코드: FailedToConnect클러스터오류코드 

### <a name="error"></a>Error

"크기 조정 작업을 수행하기 위해 클러스터 관리 끝점에 연결할 수 없습니다. 네트워크 보안 규칙이 클러스터에 대한 외부 액세스를 차단하지 않고 클러스터 관리자(Ambari) UI에 성공적으로 액세스할 수 있는지 확인합니다."

### <a name="cause"></a>원인

NSG(네트워크 보안 그룹)의 방화벽 규칙이 중요한 Azure 상태 및 관리 서비스와 클러스터 통신을 차단하고 있습니다.

### <a name="resolution"></a>해결 방법

네트워크 보안 그룹을 사용하여 네트워크 트래픽을 제어하려는 경우 HDInsight를 설치하기 전에 다음 작업을 수행하십시오.

- HDInsight에 대해 사용할 Azure 지역을 식별합니다.
- HDInsight에 필요한 IP 주소를 식별합니다. 자세한 내용은 [HDInsight 관리 IP 주소](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)를 참조하세요.
  - HDInsight를 설치하려는 서브넷에 대한 네트워크 보안 그룹을 만들거나 수정합니다.
  - 네트워크 보안 그룹의 경우 IP 주소에서 포트 443에서 인바운드 트래픽을 허용합니다. 이 구성을 통해 HDInsight 관리 서비스가 가상 네트워크 외부에서 클러스터에 도달할 수 있습니다.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>오류 코드: 저장소권한차단ForMsi

### <a name="error"></a>Error

"관리되는 ID에는 저장소 계정에 대한 사용 권한이 없습니다. '저장소 Blob 데이터 소유자' 역할이 저장소 계정의 관리 ID에 할당되어 있는지 확인하십시오. 스토리지: /구독/ \<구독\> ID/리소스그룹/\< \> 리소스 그룹 \<이름/공급자/Microsoft.Storage계정/저장소 계정 이름,\>관리되는 ID: /구독/구독 \<ID/리소스\> 그룹/리소스 그룹\< 이름/공급자/Microsoft.ManagedIdentity/userAssignedIdentity/사용자\> \<관리 ID 이름\>"

### <a name="cause"></a>원인

ID를 관리하는 데 필요한 권한을 제공하지 않았습니다. 사용자가 할당한 관리되는 ID에는 Azure Data Lake Storage Gen2 저장소 계정에 Blob 저장소 기여자 역할이 없습니다.

### <a name="resolution"></a>해결 방법

1. Azure Portal을 엽니다.
1. 스토리지 계정으로 이동합니다.
1. 액세스 **제어(IAM)를**참조하십시오.
1. 사용자에게 저장소 Blob 데이터 기여자 역할 또는 저장소 Blob 데이터 소유자 역할이 할당되어 있는지 확인합니다.

자세한 내용은 [Data Lake Storage Gen2 계정의 관리ID에 대한 권한 설정을](hdinsight-hadoop-use-data-lake-storage-gen2.md)참조하십시오.

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>오류 코드: 잘못된 네트워크 보안그룹 보안규칙

### <a name="error"></a>Error

"네트워크 보안 그룹의 보안 규칙 /\<\>구독ID / 리소스 그룹 /\> <리소스 그룹 이름 기본 /\<공급자 /\> Microsoft.Network / networkSecurityGroups / 네트워크 보안 그룹 이름은\<서브넷 / 구독 / 구독ID\>/ 리소스 그룹 /\<리소스 그룹 이름\> RG - westeurope-vnet - tomtom-default / 공급자 / Microsoft.Network / 가상 네트워크 / 가상 네트워크\<\>/ 서브 넷 /\<서브 넷 / 서브 넷 이름에서 사용되지\> 않습니다. 자세한 내용은 Azure [HDInsight에 대한 가상 네트워크 계획을](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)방문하거나 지원팀에 문의하십시오."

### <a name="cause"></a>원인

네트워크 보안 그룹 또는 사용자 정의 경로(DR)가 HDInsight 클러스터에 대한 인바운드 트래픽을 제어하는 경우 클러스터가 중요한 Azure 상태 및 관리 서비스와 통신할 수 있는지 확인합니다.

### <a name="resolution"></a>해결 방법

네트워크 보안 그룹을 사용하여 네트워크 트래픽을 제어하려는 경우 HDInsight를 설치하기 전에 다음 작업을 수행하십시오.

- HDInsight에 사용할 Azure 지역을 식별하고 해당 지역의 IP 주소의 안전한 목록을 만듭니다. 자세한 내용은 [상태 및 관리 서비스: 특정 지역](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)을 참조하십시오.
- HDInsight에 필요한 IP 주소를 식별합니다. 자세한 내용은 [HDInsight 관리 IP 주소를](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)참조하십시오.
- HDInsight를 설치하려는 서브넷에 대한 네트워크 보안 그룹을 만들거나 수정합니다. 네트워크 보안 그룹의 경우 IP 주소에서 포트 443에서 인바운드 트래픽을 허용합니다. 이 구성을 통해 HDInsight 관리 서비스가 가상 네트워크 외부에서 클러스터에 도달할 수 있습니다.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>오류 코드: 클러스터 설정이 하나 이상의 호스트에 구성 요소를 설치하지 못했습니다.

### <a name="error"></a>Error

"클러스터 설정이 하나 이상의 호스트에 구성 요소를 설치하지 못했습니다. 요청을 다시 시도하십시오."

### <a name="cause"></a>원인 

일반적으로 이 오류는 일시적인 문제 또는 Azure 중단이 있을 때 발생합니다.

### <a name="resolution"></a>해결 방법

클러스터 배포에 영향을 줄 수 있는 Azure 중단에 대해 [Azure 상태](https://status.azure.com) 페이지를 확인합니다. 중단이 없는 경우 클러스터 배포를 다시 시도합니다.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>오류 코드: FailedToConnect클러스터오류코드

### <a name="error"></a>Error

클러스터 관리 끝점에 연결할 수 없습니다. 나중에 다시 시도하십시오.

### <a name="cause"></a>원인

클러스터를 만들 때 HDInsight 서비스가 클러스터에 연결할 수 없습니다.

### <a name="resolution"></a>해결 방법

사용자 지정 VNet 네트워크 보안 그룹(NSG) 및 사용자 정의 경로(DR)를 사용하는 경우 클러스터가 HDInsight 관리 서비스와 통신할 수 있는지 확인합니다. 자세한 내용은 [HDInsight 관리 IP 주소를](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)참조하십시오.

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>오류 코드: 정책 위반으로 인해 배포가<Resource URI>실패했습니다. 정책 식별자: '[{"policyAssignment":{"name":"":"/공급자/Microsoft.ManagementGroups/공급자/공급자.권한<Policy Name> <Management Group Name> 부여/정책할당/<Policy Name>"},"정책정의":<Policy Definition>

### <a name="cause"></a>원인

구독 기반 Azure 정책은 공용 IP 주소 생성을 거부할 수 있습니다. HDInsight 클러스터를 생성하려면 두 개의 공용 IP가 필요합니다.

다음 정책은 일반적으로 클러스터 생성에 영향을 미칩니다.

* 구독 내에서 IP 주소 또는 로드 밸러버를 만들지 않는 정책입니다.
* 저장소 계정을 만들지 못하게 하는 정책입니다.
* IP 주소 또는 로드 밸러저와 같은 네트워킹 리소스를 삭제하지 않는 정책입니다.

### <a name="resolution"></a>해결 방법

HDInsight 클러스터를 만드는 동안 구독 기반 Azure 정책을 삭제하거나 사용하지 않도록 설정합니다.

---

## <a name="next-steps"></a>다음 단계

클러스터 생성 시 오류 해결에 대한 자세한 내용은 [Azure HDInsight를 통해 클러스터 생성 실패 문제를 해결합니다.](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)
