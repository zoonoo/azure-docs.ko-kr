---
title: 세분화된 역할 기반 액세스 Azure HDInsight 클러스터 구성
description: HDInsight 클러스터 구성에 대한 세분화된 역할 기반 액세스로마이그레이션의 일부로 필요한 변경 사항에 대해 알아봅니다.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: f1fdb9dffbe06430ea7e3eb9339e23f5239e4e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310835"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>클러스터 구성에 대한 세밀한 역할 기반 액세스로 마이그레이션

중요한 정보를 얻기 위해 보다 세분화된 역할 기반 액세스를 지원하기 위해 몇 가지 중요한 변경 사항을 도입했습니다. 이러한 변경 사항의 일부로 [영향을 받는 엔터티/시나리오](#am-i-affected-by-these-changes)중 하나를 사용하는 경우 **2019년 9월 3일까지** 일부 작업이 필요할 수 있습니다.

## <a name="what-is-changing"></a>변경되는 내용

이전에는 `*/read` 소유자, 기여자 또는 리더 [RBAC 역할을](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)소유한 클러스터 사용자가 HDInsight API를 통해 보안을 사용할 수 있었으며 권한이 있는 모든 사용자가 사용할 수 있었습니다. 암호는 사용자의 역할이 허용하는 것보다 더 높은 액세스 권한을 얻는 데 사용할 수 있는 값으로 정의됩니다. 여기에는 클러스터 게이트웨이 HTTP 자격 증명, 저장소 계정 키 및 데이터베이스 자격 증명과 같은 값이 포함됩니다.

2019년 9월 3일부터 이러한 비밀에 `Microsoft.HDInsight/clusters/configurations/action` 액세스하려면 권한이 필요하므로 Reader 역할을 가진 사용자가 더 이상 액세스할 수 없습니다. 이 권한이 있는 역할은 기여자, 소유자 및 새 HDInsight 클러스터 운영자 역할입니다(자세한 내용은 아래 참조).

또한 기여자 또는 소유자의 관리 권한을 부여하지 않고 비밀을 검색할 수 있는 새로운 [HDInsight 클러스터 운영자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) 역할을 도입합니다. 요약하면

| 역할                                  | 이전의                                                                                        | 앞으로 나아가기       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 판독기                                | - 비밀을 포함하여 액세스 읽기                                                                   | - 비밀을 **제외한** 액세스 읽기 |           |   |   |
| HDInsight 클러스터 운영자<br>(새 역할) | 해당 없음                                                                                              | - 읽기 / 쓰기 액세스, 비밀을 포함         |   |   |
| 참가자                           | - 읽기 / 쓰기 액세스, 비밀을 포함<br>- 모든 유형의 Azure 리소스를 만들고 관리합니다.     | 변경 내용 없음 |
| 소유자                                 | - 읽기 / 비밀을 포함한 액세스 쓰기<br>- 모든 자원에 대한 전체 액세스<br>- 다른 사람에게 액세스 위임 | 변경 내용 없음 |

클러스터 비밀에 대한 읽기/쓰기 액세스 권한을 부여하기 위해 사용자에게 HDInsight 클러스터 운영자 역할 할당을 추가하는 방법에 대한 자세한 내용은 아래 섹션을 [참조하십시오.](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)

## <a name="am-i-affected-by-these-changes"></a>이러한 변경 사항의 영향을 받습니까?

다음 엔터티 및 시나리오는 영향을 받습니다.

- [API](#api): `/configurations` 또는 `/configurations/{configurationName}` 끝점을 사용하는 사용자입니다.
- 비주얼 스튜디오 코드 버전 1.1.1 [이하에 대한 Azure HDInsight 도구.](#azure-hdinsight-tools-for-visual-studio-code)
- [IntelliJ](#azure-toolkit-for-intellij) 버전 3.20.0 이하의 Azure 도구 키트.
- 버전 2.3.9000.1 [아래의 Visual Studio용 Azure 데이터 레이크 및 스트림 분석 도구입니다.](#azure-data-lake-and-stream-analytics-tools-for-visual-studio)
- [이클립스](#azure-toolkit-for-eclipse) 버전 3.15.0 이하의 Azure 도구 키트.
- [.NET용 SDK](#sdk-for-net)
    - [버전 1.x 또는 2.x](#versions-1x-and-2x): `GetClusterConfigurations` `GetConnectivitySettings`ConfigurationsOperationsExtensions 클래스의 ". `ConfigureHttpSettings` `EnableHttp` `DisableHttp`
    - [버전 3.x 이상](#versions-3x-and-up) `Get`: `Update` `EnableHttp` `ConfigurationsOperationsExtensions` 클래스의 " `DisableHttp` 또는 메서드를 사용하는 사용자입니다.
- [파이썬용 SDK](#sdk-for-python): `get` `update` 클래스의 `ConfigurationsOperations` 또는 메서드를 사용하는 사용자입니다.
- [Java용 SDK](#sdk-for-java): `update` 클래스의 `get` 또는 `ConfigurationsInner` 메서드를 사용하는 사용자입니다.
- [이동용 SDK](#sdk-for-go): `Get` 구조체의 `Update` `ConfigurationsClient` 또는 메서드를 사용하는 사용자입니다.
- 버전 2.0.0 [이하의 Az.HDInsight 파워쉘.](#azhdinsight-powershell)
시나리오의 마이그레이션 단계를 보려면 아래 섹션(또는 위의 링크를 사용)을 참조하십시오.

### <a name="api"></a>API

다음 API는 변경되거나 더 이상 사용되지 않습니다.

- [**GET /구성/{구성이름}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (중요한 정보가 제거됨)
    - 이전에는 개별 구성 유형(비밀 포함)을 가져오는 데 사용되었습니다.
    - 2019년 9월 3일부터 이 API 호출은 이제 비밀이 생략된 개별 구성 유형을 반환합니다. 비밀을 포함한 모든 구성을 얻으려면 새 POST /configurations 호출을 사용합니다. 게이트웨이 설정만 가져오려면 새 POST /getGatewaySettings 호출을 사용합니다.
- [**GET /구성(더**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) 이상 사용되지 않습니다)
    - 이전에 모든 구성(비밀 포함)을 얻는 데 사용되었습니다.
    - 2019년 9월 3일부터 이 API 호출은 더 이상 사용되지 않으며 더 이상 지원되지 않습니다. 앞으로 모든 구성을 얻으려면 새 POST /구성 호출을 사용합니다. 중요한 매개 변수가 생략된 구성을 얻으려면 GET /configurations/{configurationName} 호출을 사용합니다.
- [**POST /구성/{구성이름}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (더 이상 사용되지)
    - 이전에 게이트웨이 자격 증명을 업데이트하는 데 사용되었습니다.
    - 2019년 9월 3일부터 이 API 호출은 더 이상 사용되지 않으며 더 이상 지원되지 않습니다. 대신 새 POST /updateGatewaySettings를 사용합니다.

다음과 같은 대체 API가 추가되었습니다.</span>

- [**포스트 /구성**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 이 API를 사용하여 비밀을 포함한 모든 구성을 가져옵니다.
- [**포스트 /get게이트웨이설정**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 이 API를 사용하여 게이트웨이 설정을 가져옵니다.
- [**포스트 /업데이트게이트웨이설정**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 이 API를 사용하여 게이트웨이 설정(사용자 이름 및/또는 암호)을 업데이트합니다.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>비주얼 스튜디오 코드를 위한 Azure HDInsight 도구

버전 1.1.1 이하를 사용하는 경우 중단을 방지하기 위해 [Visual Studio 코드용 Azure HDInsight 도구의 최신 버전으로](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) 업데이트합니다.

### <a name="azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트

버전 3.20.0 이하를 사용하는 경우 중단을 방지하기 위해 [IntelliJ 플러그인용 Azure 도구 키트의 최신 버전으로](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) 업데이트합니다.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>비주얼 스튜디오용 Azure 데이터 레이크 및 스트림 분석 도구

중단을 방지하기 위해 [Azure 데이터 레이크 및 Visual Studio용 스트림 분석 도구의](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) 버전 2.3.9000.1 이상으로 업데이트합니다.  업데이트에 대한 도움말은 [설명서, Visual Studio용 데이터 레이크 도구 업데이트](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)를 참조하십시오.

### <a name="azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트

버전 3.15.0 이하를 사용하는 경우 중단을 방지하기 위해 [Eclipse용 Azure 도구 키트의 최신 버전으로](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) 업데이트합니다.

### <a name="sdk-for-net"></a>.NET용 SDK

#### <a name="versions-1x-and-2x"></a>버전 1.x 및 2.x

.NET에 대한 HDInsight SDK의 [버전 2.1.0으로](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) 업데이트합니다. 이러한 변경 의 영향을 받는 메서드를 사용하는 경우 최소한의 코드 수정이 필요할 수 있습니다.

- `ClusterOperationsExtensions.GetClusterConfigurations`더 이상 저장소 키(코어 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 **중요한 매개 변수를 반환하지** 않습니다.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 사용합니다. `ClusterOperationsExtensions.ListConfigurations`  'Reader' 역할을 가진 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 사용자가 클러스터에 대한 중요한 정보에 액세스할 수 있는 정보를 세부적으로 제어할 수 있습니다.
    - HTTP 게이트웨이 자격 증명만 `ClusterOperationsExtensions.GetGatewaySettings`검색하려면 을 사용합니다.

- `ClusterOperationsExtensions.GetConnectivitySettings`이제 더 이상 사용되지 않으며 로 `ClusterOperationsExtensions.GetGatewaySettings`대체되었습니다.

- `ClusterOperationsExtensions.ConfigureHttpSettings`이제 더 이상 사용되지 않으며 로 `ClusterOperationsExtensions.UpdateGatewaySettings`대체되었습니다.

- `ConfigurationsOperationsExtensions.EnableHttp`더 `DisableHttp` 이상 사용되지 않습니다. 이제 HTTP가 항상 활성화되므로 이러한 메서드가 더 이상 필요하지 않습니다.

#### <a name="versions-3x-and-up"></a>버전 3.x 이상

.NET에 대한 HDInsight SDK버전 [5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) 이상으로 업데이트합니다. 이러한 변경 의 영향을 받는 메서드를 사용하는 경우 최소한의 코드 수정이 필요할 수 있습니다.

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)더 이상 저장소 키(코어 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 **중요한 매개 변수를 반환하지** 않습니다.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 사용합니다. [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet)'Reader' 역할을 가진 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 사용자가 클러스터에 대한 중요한 정보에 액세스할 수 있는 정보를 세부적으로 제어할 수 있습니다. 
    - HTTP 게이트웨이 자격 증명만 [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)검색하려면 을 사용합니다. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)이제 더 이상 사용되지 않으며 로 [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)대체되었습니다. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)더 [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) 이상 사용되지 않습니다. 이제 HTTP가 항상 활성화되므로 이러한 메서드가 더 이상 필요하지 않습니다.

### <a name="sdk-for-python"></a>Python용 SDK

파이썬에 대한 HDInsight SDK의 [버전 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) 이상으로 업데이트합니다. 이러한 변경 의 영향을 받는 메서드를 사용하는 경우 최소한의 코드 수정이 필요할 수 있습니다.

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)더 이상 저장소 키(코어 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 **중요한 매개 변수를 반환하지** 않습니다.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 사용합니다. [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)'Reader' 역할을 가진 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 사용자가 클러스터에 대한 중요한 정보에 액세스할 수 있는 정보를 세부적으로 제어할 수 있습니다. 
    - HTTP 게이트웨이 자격 증명만 [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)검색하려면 을 사용합니다.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)이제 더 이상 사용되지 않으며 로 [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)대체되었습니다.

### <a name="sdk-for-java"></a>자바를위한 SDK

Java용 HDInsight SDK 버전 [1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) 이상으로 업데이트합니다. 이러한 변경 의 영향을 받는 메서드를 사용하는 경우 최소한의 코드 수정이 필요할 수 있습니다.

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)더 이상 저장소 키(코어 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 **중요한 매개 변수를 반환하지** 않습니다.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)이제 더 이상 사용되지 않습니다.

### <a name="sdk-for-go"></a>이동을위한 SDK

버전 [27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) 이상으로 업데이트이동HDInsight SDK의 이상. 이러한 변경 의 영향을 받는 메서드를 사용하는 경우 최소한의 코드 수정이 필요할 수 있습니다.

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)더 이상 저장소 키(코어 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 **중요한 매개 변수를 반환하지** 않습니다.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 사용합니다. [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List)'Reader' 역할을 가진 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 사용자가 클러스터에 대한 중요한 정보에 액세스할 수 있는 정보를 세부적으로 제어할 수 있습니다. 
    - HTTP 게이트웨이 자격 증명만 [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)검색하려면 을 사용합니다.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)이제 더 이상 사용되지 않으며 로 [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)대체되었습니다.

### <a name="azhdinsight-powershell"></a>아즈.HDInsight 파워쉘
중단을 방지하기 위해 [Az PowerShell 버전 2.0.0](https://www.powershellgallery.com/packages/Az) 이상으로 업데이트합니다.  이러한 변경 의 영향을 받는 메서드를 사용하는 경우 최소한의 코드 수정이 필요할 수 있습니다.
- `Grant-AzHDInsightHttpServicesAccess`이제 더 이상 사용되지 않으며 새 `Set-AzHDInsightGatewayCredential` cmdlet으로 대체되었습니다.
- `Get-AzHDInsightJobOutput`저장소 키에 대한 세부적인 역할 기반 액세스를 지원하도록 업데이트되었습니다.
    - HDInsight 클러스터 운영자, 기여자 또는 소유자 역할이 있는 사용자는 영향을 받지 않습니다.
    - Reader 역할만 있는 사용자는 매개 `DefaultStorageAccountKey` 변수를 명시적으로 지정해야 합니다.
- `Revoke-AzHDInsightHttpServicesAccess`이제 더 이상 사용되지 않습니다. 이제 HTTP가 항상 활성화되므로 이 cmdlet이 더 이상 필요하지 않습니다.
 [az를 참조하십시오. 자세한](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) 내용은 HDInsight 마이그레이션 가이드입니다.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>사용자에게 HDInsight 클러스터 운영자 역할 할당 추가

[소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 역할을 가진 사용자는 중요한 [HDInsight 클러스터](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) 구성 값(예: 클러스터 게이트웨이 자격 증명 및 저장소 계정 키)에 대한 읽기/쓰기 액세스를 원하는 사용자에게 HDInsight 클러스터 운영자 역할을 할당할 수 있습니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

이 역할 할당을 추가하는 가장 간단한 `az role assignment create` 방법은 Azure CLI의 명령을 사용하는 것입니다.

> [!NOTE]
> 이 명령은 이러한 권한을 부여할 수 있기 때문에 소유자 역할이 있는 사용자가 실행해야 합니다. 은 `--assignee` HDInsight 클러스터 운영자 역할을 할당할 사용자의 서비스 주체 또는 전자 메일 주소의 이름입니다. 사용 권한 오류가 부족하면 아래 FAQ를 참조하십시오.

#### <a name="grant-role-at-the-resource-cluster-level"></a>리소스(클러스터) 수준에서 역할 부여

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>리소스 그룹 수준에서 부여 역할

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>구독 수준에서 부여 역할

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure Portal 사용

또는 Azure 포털을 사용하여 사용자에게 HDInsight 클러스터 운영자 역할 할당을 추가할 수 있습니다. 설명서, [RBAC 및 Azure 포털을 사용하여 Azure 리소스에 대한 액세스 관리 - 역할 할당 추가](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)를 참조하십시오.

## <a name="faq"></a>FAQ

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>API 요청 및/또는 도구를 업데이트한 후 403(사용할 수 없음) 응답이 표시되는 이유는 무엇인가요?

클러스터 구성은 이제 세분화된 역할 기반 액세스 `Microsoft.HDInsight/clusters/configurations/*` 제어 뒤에 있으며 해당 제어에 액세스할 수 있는 권한이 필요합니다. 이 권한을 얻으려면 구성에 액세스하려는 사용자 또는 서비스 주체에게 HDInsight 클러스터 운영자, 기여자 또는 소유자 역할을 할당합니다.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>다른 사용자 또는 서비스 주체에 HDInsight 클러스터 운영자 역할을 할당하기 위해 Azure CLI 명령을 실행할 때 "작업을 완료하기에 부족한 권한"이 표시되는 이유는 무엇입니까?

명령을 실행하는 사용자 또는 서비스 주체는 소유자 역할을 하는 것 외에도 할당자의 개체 아이디를 조회할 수 있는 충분한 AAD 권한이 있어야 합니다. 이 메시지는 AAD 권한이 부족했음을 나타냅니다. `-–assignee` 인수를 교체하고 `–assignee-object-id` 이름 대신 매개 변수(또는 관리되는 ID의 경우 주체 ID)로 지정인의 개체 ID를 제공합니다. 자세한 내용은 az 역할 [할당 생성 설명서의](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) 선택적 매개 변수 섹션을 참조하십시오.

그래도 작동하지 않으면 AAD 관리자에게 문의하여 올바른 권한을 획득하십시오.

### <a name="what-will-happen-if-i-take-no-action"></a>아무 조치도 취하지 않으면 어떻게 됩니까?

2019년 `GET /configurations` 9월 3일부터 `POST /configurations/gateway` 통화는 더 이상 정보를 `GET /configurations/{configurationName}` 반환하지 않으며 통화는 저장소 계정 키 또는 클러스터 암호와 같은 중요한 매개 변수를 더 이상 반환하지 않습니다. 해당 SDK 메서드및 PowerShell cmdlet도 마찬가지입니다.

위에서 언급한 Visual Studio, VSCode, IntelliJ 또는 Eclipse용 도구 중 하나의 이전 버전을 사용하는 경우 업데이트할 때까지 더 이상 작동하지 않습니다.

자세한 내용은 시나리오에 대한 이 문서의 해당 섹션을 참조하십시오.
