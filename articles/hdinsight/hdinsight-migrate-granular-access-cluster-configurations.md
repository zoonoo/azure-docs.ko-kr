---
title: 클러스터 구성-Azure HDInsight에 대 한 세분화 된 역할 기반 액세스로 마이그레이션
description: HDInsight 클러스터 구성에 대 한 세분화 된 역할 기반 액세스 마이그레이션의 일환으로 필요한 변경에 알아봅니다.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1ec4786291d6e2e5be6785e52cf3ab5bb5bbc690
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754542"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>클러스터 구성에 대한 세밀한 역할 기반 액세스로 마이그레이션

더 세분화 된 역할 기반 액세스를 지원 하도록 중요 한 정보를 가져올 일부 중요 한 변경 내용을 도입 되었습니다. 이 중 일부를 변경 되 면 일부 **작업이 필요할 수 있습니다** 중 하나를 사용 하는 경우를 [엔터티/시나리오를 영향을 받는](#am-i-affected-by-these-changes)합니다.

## <a name="what-is-changing"></a>변경되는 내용

이전에 암호를 얻을 수는 HDInsight API를 통해 소유자, 참가자 또는 읽기 권한자를 소유 하는 클러스터 사용자 [RBAC 역할](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)처럼 가진 사람이 면 누구나 사용할 수 있었던는 `*/read` 권한이 필요 했습니다.
앞으로 이러한 비밀에 액세스 해야 합니다는 `Microsoft.HDInsight/clusters/configurations/*` 권한, 즉 읽기 권한자 역할을 사용 하 여 사용자가 더 이상 액세스할 수 없습니다. 암호는 사용자의 역할 보다 더 높은 액세스를 가져오는 데 사용할 수 있는 값을 허용 해야 하는 대로 정의 됩니다. 여기에 클러스터 게이트웨이 HTTP 자격 증명, 저장소 계정 키 및 데이터베이스 자격 증명 등의 값이 포함 됩니다.

새 도입 [HDInisght 클러스터 연산자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) 참가자 또는 소유자의 관리 권한을 부여 하지 않고 암호를 검색할 수 있는 역할입니다. 요약하면

| 역할                                  | 이전에                                                                                       | 앞으로       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 판독기                                | 읽기 액세스 암호를 포함 하 여                                                                   | 읽기 액세스 **제외** 비밀 |           |   |   |
| HDInsight 클러스터 연산자<br>(새 역할) | N/A                                                                                              | -암호를 포함 하 여 읽기/쓰기 액세스         |   |   |
| 참가자                           | -암호를 포함 하 여 읽기/쓰기 액세스<br>생성 하 고 모든 유형의 Azure 리소스를 관리 합니다.     | 변경 내용 없음 |
| 소유자                                 | -암호를 포함 하 여 읽기/쓰기 액세스<br>-모든 리소스에 대 한 액세스 전체<br>--다른 사용자의 액세스를 위임 하는 중 | 변경 내용 없음 |

권한을 부여 하도록 사용자에 게 HDInsight 클러스터 운영자 역할 할당을 추가 하는 방법에 대 한 정보에 대 한 읽기/쓰기 액세스 클러스터 암호를 참조 하십시오는 섹션 아래 [사용자에 게 HDInsight 클러스터 운영자 역할 할당 추가](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)합니다.

## <a name="am-i-affected-by-these-changes"></a>이러한 변경으로 영향을 받습니까?

다음 엔터티 및 시나리오는 영향을 받습니다.

- [API](#api): 사용 하 여 사용자를 `/configurations` 또는 `/configurations/{configurationName}` 끝점입니다.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 버전 1.1.1 이하로입니다.
- [IntelliJ 용 azure 도구 키트](#azure-toolkit-for-intellij) 3.20.0 버전 이하로입니다.
- [Azure Data Lake 및 Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) 2.3.9000.1 버전 보다 낮음.
- [Eclipse 용 azure 도구 키트](#azure-toolkit-for-eclipse) 3.15.0 버전 이하로입니다.
- [SDK for .NET](#sdk-for-net)
    - [버전 1.x 또는 2.x](#versions-1x-and-2x): 사용 하 여 사용자를 `GetClusterConfigurations`, `GetConnectivitySettings`를 `ConfigureHttpSettings`를 `EnableHttp` 또는 `DisableHttp` ConfigurationsOperationsExtensions 클래스에서 메서드.
    - [버전 3.x 이상](#versions-3x-and-up): 사용 하 여 사용자를 `Get`, `Update`, `EnableHttp`, 또는 `DisableHttp` 메서드에서 `ConfigurationsOperationsExtensions` 클래스입니다.
- [Python 용 SDK](#sdk-for-python): 사용 하 여 사용자를 `get` 또는 `update` 메서드에서 `ConfigurationsOperations` 클래스입니다.
- [Java 용 SDK](#sdk-for-java): 사용 하 여 사용자를 `update` 또는 `get` 메서드에서 `ConfigurationsInner` 클래스입니다.
- [SDK for Go](#sdk-for-go): 사용 하 여 사용자를 `Get` 또는 `Update` 메서드에서 `ConfigurationsClient` 구조체입니다.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) 버전 2.0.0 아래.
해당 시나리오의 마이그레이션 단계를 확인하려면 아래 섹션을 참조하세요(또는 위의 링크 사용).

### <a name="api"></a>API

다음과 같은 Api 변경 되거나 사용 되지 않습니다.

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (중요 한 정보가 제거)
    - 개별 구성 유형 (암호 포함)을 가져오려면 이전에 사용 합니다.
    - 이 API 호출은 이제 생략 하면 암호를 사용 하 여 개별 구성 유형을 반환 합니다. 비밀을 포함 한 모든 구성을 가져오려면 새 POST /configurations 호출을 사용 합니다. 게이트웨이 설정만 가져오려면 새 POST /getGatewaySettings 호출을 사용 합니다.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (사용 되지 않음)
    - 이전에 모든 구성 (암호 포함)를 가져오는 데
    - 이 API 호출은 더 이상 지원 되지 않습니다. 모든 구성 앞으로 가져오려면 새 POST /configurations 호출을 사용 합니다. 를 생략 하는 중요 한 매개 변수를 사용 하 여 구성을 가져오려면 GET /configurations/ {configurationName} 호출을 사용 합니다.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (사용 되지 않음)
    - 이전에 게이트웨이 자격 증명을 업데이트 하는 데 사용 합니다.
    - 이 API 호출 않으며 더 이상 지원 되지 않습니다. 새 게시물 /updateGatewaySettings를 대신 사용 합니다.

다음 대체 Api가 추가 되었습니다.</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 이 API를 사용 하 여 암호를 비롯 한 모든 구성을 가져오려고 합니다.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 이 API를 사용 하 여 게이트웨이 설정을 가져올 수 있습니다.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 이 API를 사용 하 여 (사용자 이름 및/또는 암호) 게이트웨이 설정을 업데이트 합니다.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

버전 1.1.1를 사용 하는 경우 아래 업데이트 된 [최신 버전의 Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) 중단을 방지 합니다.

### <a name="azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트

아래 업데이트 하거나 3.20.0 버전을 사용 하는 경우는 [IntelliJ 플러그 인에 대 한 Azure 도구 키트의 최신 버전](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) 중단을 방지 합니다.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake 및 Stream Analytics Tools for Visual Studio

나중의 업데이트 버전 2.3.9000.1 [Azure Data Lake 및 Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) 중단을 방지 합니다.  업데이트할 수 있는 도움말 설명서를 참조 하세요. [Data Lake Tools for Visual Studio 업데이트](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)합니다.

### <a name="azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트

아래 업데이트 하거나 3.15.0 버전을 사용 하는 경우는 [Eclipse 용 Azure 도구 키트의 최신 버전](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) 중단을 방지 합니다.

### <a name="sdk-for-net"></a>.NET용 SDK

#### <a name="versions-1x-and-2x"></a>버전 1.x 및 2.x

업데이트 [버전 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) 의.NET에 대 한 HDInsight SDK. 이러한 변경의 영향을 받는 메서드를 사용 하는 경우에 최소한의 코드를 수정 필요할 수 있습니다.

- `ClusterOperationsExtensions.GetClusterConfigurations` 됩니다 **중요 한 매개 변수를 더 이상 반환** 예: 저장소 키 (코어 사이트) 또는 HTTP 자격 증명 (게이트웨이).
    - 모든 구성에 중요 한 매개 변수를 포함 하 여 검색을 사용 하 여 `ClusterOperationsExtensions.ListConfigurations` 앞으로 이동 합니다.  참고 '읽기' 역할을 사용 하 여 사용자는이 메서드를 사용할 수 없습니다. 이렇게 하면 세부적인 제어는 사용자가 클러스터에 대 한 중요 한 정보에 액세스할 수 있습니다.
    - 방금 HTTP 게이트웨이 자격 증명을 검색 하려면 `ClusterOperationsExtensions.GetGatewaySettings`합니다.

- `ClusterOperationsExtensions.GetConnectivitySettings` 이제 사용 되지 않으며 바뀌었습니다 `ClusterOperationsExtensions.GetGatewaySettings`합니다.

- `ClusterOperationsExtensions.ConfigureHttpSettings` 이제 사용 되지 않으며 바뀌었습니다 `ClusterOperationsExtensions.UpdateGatewaySettings`합니다.

- `ConfigurationsOperationsExtensions.EnableHttp` 및 `DisableHttp` 이제 사용 되지 않습니다. HTTP 이제 항상 설정 되어 있으므로 이러한 메서드는 더 이상 필요 합니다.

#### <a name="versions-3x-and-up"></a>버전 3.x 이상

업데이트 [버전 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) HDInsight SDK for.NET의 이상. 이러한 변경의 영향을 받는 메서드를 사용 하는 경우에 최소한의 코드를 수정 필요할 수 있습니다.

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 됩니다 **중요 한 매개 변수를 더 이상 반환** 예: 저장소 키 (코어 사이트) 또는 HTTP 자격 증명 (게이트웨이).
    - 모든 구성에 중요 한 매개 변수를 포함 하 여 검색을 사용 하 여 [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) 앞으로 이동 합니다.  참고 '읽기' 역할을 사용 하 여 사용자는이 메서드를 사용할 수 없습니다. 이렇게 하면 세부적인 제어는 사용자가 클러스터에 대 한 중요 한 정보에 액세스할 수 있습니다. 
    - 방금 HTTP 게이트웨이 자격 증명을 검색 하려면 [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)합니다. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 이제 사용 되지 않으며 바뀌었습니다 [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)합니다. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 및 [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) 이제 사용 되지 않습니다. HTTP 이제 항상 설정 되어 있으므로 이러한 메서드는 더 이상 필요 합니다.

### <a name="sdk-for-python"></a>Python용 SDK

업데이트 [버전 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) 이상의 Python 용 HDInsight SDK. 이러한 변경의 영향을 받는 메서드를 사용 하는 경우에 최소한의 코드를 수정 필요할 수 있습니다.

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) 됩니다 **중요 한 매개 변수를 더 이상 반환** 예: 저장소 키 (코어 사이트) 또는 HTTP 자격 증명 (게이트웨이).
    - 모든 구성에 중요 한 매개 변수를 포함 하 여 검색을 사용 하 여 [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) 앞으로 이동 합니다.  참고 '읽기' 역할을 사용 하 여 사용자는이 메서드를 사용할 수 없습니다. 이렇게 하면 세부적인 제어는 사용자가 클러스터에 대 한 중요 한 정보에 액세스할 수 있습니다. 
    - 방금 HTTP 게이트웨이 자격 증명을 검색 하려면 [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)합니다.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) 이제 사용 되지 않으며 바뀌었습니다 [ `ClusterOperations.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)합니다.

### <a name="sdk-for-java"></a>Java 용 SDK

업데이트 [버전 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) HDInsight SDK for Java의 이상. 이러한 변경의 영향을 받는 메서드를 사용 하는 경우에 최소한의 코드를 수정 필요할 수 있습니다.

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) 됩니다 **중요 한 매개 변수를 더 이상 반환** 예: 저장소 키 (코어 사이트) 또는 HTTP 자격 증명 (게이트웨이).
    - 모든 구성에 중요 한 매개 변수를 포함 하 여 검색을 사용 하 여 [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) 앞으로 이동 합니다.  참고 '읽기' 역할을 사용 하 여 사용자는이 메서드를 사용할 수 없습니다. 이렇게 하면 세부적인 제어는 사용자가 클러스터에 대 한 중요 한 정보에 액세스할 수 있습니다. 
    - 방금 HTTP 게이트웨이 자격 증명을 검색 하려면 [ `ClustersInner.getGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable)합니다.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) 이제 사용 되지 않으며 바뀌었습니다 [ `ClustersInner.updateGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable)합니다.

### <a name="sdk-for-go"></a>SDK For Go

업데이트 [27.1.0 버전](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) HDInsight SDK for Go의 이상. 이러한 변경의 영향을 받는 메서드를 사용 하는 경우에 최소한의 코드를 수정 필요할 수 있습니다.

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) 됩니다 **중요 한 매개 변수를 더 이상 반환** 예: 저장소 키 (코어 사이트) 또는 HTTP 자격 증명 (게이트웨이).
    - 모든 구성에 중요 한 매개 변수를 포함 하 여 검색을 사용 하 여 [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) 앞으로 이동 합니다.  참고 '읽기' 역할을 사용 하 여 사용자는이 메서드를 사용할 수 없습니다. 이렇게 하면 세부적인 제어는 사용자가 클러스터에 대 한 중요 한 정보에 액세스할 수 있습니다. 
    - 방금 HTTP 게이트웨이 자격 증명을 검색 하려면 [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)합니다.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) 이제 사용 되지 않으며 바뀌었습니다 [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)합니다.

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
업데이트 [Az PowerShell 버전 2.0.0](https://www.powershellgallery.com/packages/Az) 또는 나중에 중단을 방지 합니다.  이러한 변경의 영향을 받는 메서드를 사용 하는 경우 최소한의 코드를 수정 해야 합니다.
- `Grant-AzHDInsightHttpServicesAccess` 이제 사용 되지 않으며 새 바뀌었습니다 `Set-AzHDInsightGatewayCredential` cmdlet.
- `Get-AzHDInsightJobOutput` 저장소 키에 세분화 된 역할 기반 액세스를 지원 하도록 업데이트 되었습니다.
    - HDInsight 클러스터 연산자, 참가자 또는 소유자 역할이 있는 사용자를 받지 않습니다.
    - 사용자만 읽기 권한자 역할을 사용 하 여 지정 해야 합니다.는 `DefaultStorageAccountKey` 매개 변수가 명시적으로 합니다.
- `Revoke-AzHDInsightHttpServicesAccess` 이제 사용 되지 않습니다. HTTP 이제 항상 설정 되어 있으므로이 cmdlet은 더 이상 필요 합니다.
 참조 된 [az 합니다. HDInsight 마이그레이션 가이드](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) 대 한 자세한 내용은 합니다.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>사용자에 게 HDInsight 클러스터 운영자 역할 할당 추가

사용 하 여 사용자를 [참여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 또는 [소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 역할을 할당할 수는 [HDInisght 클러스터 연산자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) 구분에 대 한 읽기/쓰기 권한이 하려고 하는 사용자에 게 역할 HDInsight 클러스터 구성 (예: 클러스터 게이트웨이 자격 증명 및 저장소 계정 키) 값입니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

사용 하 여이 역할 할당을 추가 하는 가장 간단한 방법은는 `az role assignemnt create` Azure CLI 명령을 합니다.

> [!NOTE]
> 이러한 권한을 부여할 수 있습니다만 처럼이 명령은 참가자 또는 소유자 역할을 사용 하 여 사용자가 실행 되어야 합니다. `--assignee` HDInsight 클러스터 운영자 역할 할당 하려는 사용자의 전자 메일 주소입니다.

#### <a name="grant-role-at-the-resource-cluster-level"></a>리소스 (클러스터) 수준에서 역할에 부여

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>리소스 그룹 수준에서 역할에 부여

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>구독 수준에서 역할에 부여

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure Portal 사용

또는 사용자에 게 HDInsight 클러스터 운영자 역할 할당을 추가 하려면 Azure portal을 사용할 수 있습니다. 설명서를 참조 하세요 [RBAC 및 Azure portal을 사용 하 여 Azure 리소스에 대 한 액세스 관리-역할 할당을 추가](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)합니다.
