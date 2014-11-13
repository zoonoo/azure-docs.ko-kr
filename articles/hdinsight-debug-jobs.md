<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="HDInsight의 Hadoop 디버그: 오류 메시지 | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="PowerShell을 사용하여 HDInsight를 관리할 때 표시될 수 있는 오류 메시지와 복구를 위해 수행할 수 있는 단계에 대해 알아봅니다." services="hdinsight" title="HDInsight의 Hadoop 디버그: 오류 메시지" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight의 Hadoop 디버그: 오류 메시지

## 소개

이 문서의 항목별 오류 메시지를 통해 Azure HDInsight의 Hadoop 사용자는 Azure PowerShell을 사용하여 서비스를 관리할 때 발생할 수 있는 가능한 오류 상태에 대해 이해할 수 있으며, 오류를 복구하기 위해 수행할 수 있는 단계에 대해 도움을 받을 수 있습니다.

이러한 오류 메시지 중 일부는 Azure 포털에서 HDinsight 클러스터를 관리할 때도 발생할 수 있습니다. 하지만 이때 발생할 수 있는 다른 오류 메시지는 해당 컨텍스트에서 가능한 수정 작업의 제약 조건으로 인해 세분화하기 어렵습니다. 다른 오류 메시지는 뚜렷한 해결 방법이 있는 경우 제공됩니다. 예를 들면 매개 변수의 제약 조건이 위반된 경우 값이 입력된 상자의 오른쪽에 팝업 메시지가 표시됩니다. 여기서는 너무 많은 데이터 노드가 요청된 경우입니다. 이를 해결하는 방법은 22 이하의 허용된 값으로 숫자를 줄이는 것입니다.

![HDI.Debugging.ErrorMessages.Portal][HDI.Debugging.ErrorMessages.Portal]

Azure PowerShell 또는 Azure 포털에서 사용자에게 표시될 수 있는 오류가 [HDInsight 오류][HDInsight 오류] 섹션에 사전순 이름별로 나열되어 있으며, 오류에 대한 다음 정보를 제공하는 [오류 설명 및 완화][오류 설명 및 완화] 섹션의 항목과 연결되어 있습니다.

-   **설명**: 사용자에게 표시되는 오류 메시지
-   **완화**: 오류를 복구하기 위해 수행할 수 있는 단계

### HDInsight 오류

[AtleastOneSqlMetastoreMustBeProvided][AtleastOneSqlMetastoreMustBeProvided]
[AzureRegionNotSupported][AzureRegionNotSupported]
[ClusterContainerRecordNotFound][ClusterContainerRecordNotFound]
[ClusterDnsNameInvalidReservedWord][ClusterDnsNameInvalidReservedWord]
[ClusterNameUnavailable][ClusterNameUnavailable]
[ClusterUserNameInvalid][ClusterUserNameInvalid]
[ClusterUserNameInvalidReservedWord][ClusterUserNameInvalidReservedWord]
[ContainerNameMisMatchWithDnsName][ContainerNameMisMatchWithDnsName]
[DataNodeDefinitionNotFound][DataNodeDefinitionNotFound]
[DeploymentDeletionFailure][DeploymentDeletionFailure]
[DnsMappingNotFound][DnsMappingNotFound]
[DuplicateClusterContainerRequest][DuplicateClusterContainerRequest]
[DuplicateClusterInHostedService][DuplicateClusterInHostedService]
[FailureToUpdateDeploymentStatus][FailureToUpdateDeploymentStatus]
[HdiRestoreClusterAltered][HdiRestoreClusterAltered]
[HeadNodeConfigNotFound][HeadNodeConfigNotFound]
[HeadNodeConfigNotFound][HeadNodeConfigNotFound]
[HostedServiceCreationFailure][HostedServiceCreationFailure]
[HostedServiceHasProductionDeployment][HostedServiceHasProductionDeployment]
[HostedServiceNotFound][HostedServiceNotFound]
[HostedServiceWithNoDeployment][HostedServiceWithNoDeployment]
[InsufficientResourcesCores][InsufficientResourcesCores]
[InsufficientResourcesHostedServices][InsufficientResourcesHostedServices]
[InternalErrorRetryRequest][InternalErrorRetryRequest]
[InvalidAzureStorageLocation][InvalidAzureStorageLocation]
[InvalidNodeSizeForDataNode][InvalidNodeSizeForDataNode]
[InvalidNodeSizeForHeadNode][InvalidNodeSizeForHeadNode]
[InvalidRightsForDeploymentDeletion][InvalidRightsForDeploymentDeletion]
[InvalidStorageAccountBlobContainerName][InvalidStorageAccountBlobContainerName]
[InvalidStorageAccountConfigurationSecretKey][InvalidStorageAccountConfigurationSecretKey]
[InvalidVersionHeaderFormat][InvalidVersionHeaderFormat]
[MoreThanOneHeadNode][MoreThanOneHeadNode]
[OperationTimedOutRetryRequest][OperationTimedOutRetryRequest]
[ParameterNullOrEmpty][ParameterNullOrEmpty]
[PreClusterCreationValidationFailure][PreClusterCreationValidationFailure]
[RegionCapabilityNotAvailable][RegionCapabilityNotAvailable]
[StorageAccountNotColocated][StorageAccountNotColocated]
[SubscriptionIdNotActive][SubscriptionIdNotActive]
[SubscriptionIdNotFound][SubscriptionIdNotFound]
[UnableToResolveDNS][UnableToResolveDNS]
[UnableToVerifyLocationOfResource][UnableToVerifyLocationOfResource]
[VersionCapabilityNotAvailable][VersionCapabilityNotAvailable]
[VersionNotSupported][VersionNotSupported]
[VersionNotSupportedInRegion][VersionNotSupportedInRegion]
[WasbAccountConfigNotFound][WasbAccountConfigNotFound]

## <span id="discription-mitigation-errors"></span></a>오류의 진단 및 완화

### <span id="AtleastOneSqlMetastoreMustBeProvided"></span></a>AtleastOneSqlMetastoreMustBeProvided

-   **설명**: Hive 및 Oozie 메타스토어에 사용자 지정 설정을 사용하려면 하나 이상의 구성 요소에 대한 Azure SQL 데이터베이스 세부 정보를 제공하세요.
-   **완화**: 사용자가 올바른 SQL Azure 메타스토어를 제공하고 요청을 다시 시도해야 합니다.

### <span id="AzureRegionNotSupported"></span></a>AzureRegionNotSupported

-   **설명**: *지역이름* 지역에서 클러스터를 만들 수 없습니다. 올바른 HDInsight 지역을 사용하여 요청을 다시 시도하세요.
-   **완화**: 현재 지원되는 클러스터 지역인 동남아시아, 서유럽, 북유럽, 미국 동부 또는 미국 서버 중에서 만들어야 합니다.

### <span id="ClusterContainerRecordNotFound"></span></a>ClusterContainerRecordNotFound

-   **설명**: 서버에서 요청된 클러스터 기록을 찾을 수 없습니다.
-   **완화**: 작업을 다시 시도하세요.

### <span id="ClusterDnsNameInvalidReservedWord"></span></a>ClusterDnsNameInvalidReservedWord

-   **설명**: 클러스터 DNS 이름인 *Dns이름*이(가) 잘못되었습니다. 이름이 영숫자로 시작하고 끝나는지 확인하세요. 이름에는 '-' 특수 문자만 포함할 수 있습니다.
-   **완화**: 클러스터에 이름의 시작과 끝이 영숫자이고 대시('-') 이외의 특수 문자가 포함되지 않은 올바른 DNS 이름을 사용했는지 확인한 후 작업을 다시 시도하세요.

### <span id="ClusterNameUnavailable"></span></a>ClusterNameUnavailable

-   **설명**: 클러스터 이름인 *클러스터이름*을(를) 사용할 수 없습니다. 다른 이름을 선택하세요.
-   **완화**: 사용자가 고유하며 기존에 존재하지 않던 클러스터 이름을 지정한 후 다시 시도해야 합니다. 사용자가 포털을 사용하는 경우 클러스터 이름을 만드는 단계에서 이미 사용 중인 이름을 선택하면 UI에서 이를 알려 줍니다.

### <span id="ClusterPasswordInvalid"></span></a>ClusterPasswordInvalid

-   **설명**: 클러스터 암호가 잘못되었습니다. 암호는 길이가 10자 이상이어야 하고, 숫자, 대문자, 소문자 및 특수 문자를 적어도 하나씩 포함해야 하고, 공백이 없어야 하며, 사용자 이름을 포함하지 않아야 합니다.
-   **완화**: 올바른 클러스터 암호를 지정한 후 작업을 다시 시도하세요.

### <span id="ClusterUserNameInvalid"></span></a>ClusterUserNameInvalid

-   **설명**: 클러스터 사용자 이름이 잘못되었습니다. 사용자 이름에 특수 문자 또는 공백이 포함되지 않았는지 확인하세요.
-   **완화**: 올바른 클러스터 사용자 이름을 지정한 후 작업을 다시 시도하세요.

### <span id="ClusterUserNameInvalidReservedWord"></span></a>ClusterUserNameInvalidReservedWord

-   **설명**: 클러스터 DNS 이름인 *클러스터이름*이(가) 잘못되었습니다. 이름이 영숫자로 시작하고 끝나는지 확인하세요. 이름에는 '-' 특수 문자만 포함할 수 있습니다.
-   **완화**: 올바른 DNS 클러스터 사용자 이름을 지정한 후 작업을 다시 시도하세요.

### <span id="ContainerNameMisMatchWithDnsName"></span></a>ContainerNameMisMatchWithDnsName

-   **설명**: 요청 본문에서 URI의 컨테이너 이름인 *컨테이너URI*와 DNS 이름인 *Dns이름*이(가) 동일해야 합니다.
-   **완화**: 컨테이너 이름과 DNS 이름이 동일한지 확인하고 작업을 다시 시도하세요.

### <span id="DataNodeDefinitionNotFound"></span></a>DataNodeDefinitionNotFound

-   **설명**: 클러스터 구성이 잘못되었습니다. 노드 크기에서 데이터 노드 정의를 찾을 수 없습니다.
-   **완화**: 작업을 다시 시도하세요.

### <span id="DeploymentDeletionFailure"></span></a>DeploymentDeletionFailure

-   **설명**: 클러스터에서 배포를 삭제하지 못했습니다.
-   **완화**: 삭제 작업을 다시 시도하세요.

### <span id="DnsMappingNotFound"></span></a>DnsMappingNotFound

-   **설명**: 서비스 구성 오류입니다. 필수 DNS 매핑 정보를 찾지 못했습니다.
-   **완화**: 클러스터를 삭제하고 새 클러스터를 만드세요.

### <span id="DuplicateClusterContainerRequest"></span></a>DuplicateClusterContainerRequest

-   **설명**: 클러스터 컨테이너 만들기 시도가 중복되었습니다. *컨테이너이름*의 레코드가 존재하지만 Etags가 일치하지 않습니다.
-   **완화**: 컨테이너에 고유한 이름을 지정한 후 만들기 작업을 다시 시도하세요.

### <span id="DuplicateClusterInHostedService"></span></a>DuplicateClusterInHostedService

-   **설명**: 호스티드 서비스인 *호스티드서비스이름*에 클러스터가 이미 포함되어 있습니다. 호스티드 서비스에는 여러 클러스터를 포함할 수 없습니다.
-   **완화**: 클러스터를 다른 호스티드 서비스에서 호스트하세요.

### <span id="FailureToUpdateDeploymentStatus"></span></a>FailureToUpdateDeploymentStatus

-   **설명**: 서버에서 클러스터 배포 상태를 업데이트할 수 없습니다.
-   **완화**: 작업을 다시 시도하세요. 이 오류가 여러 번 발생하는 경우 CSS에 문의하세요.

### <span id="HdiRestoreClusterAltered"></span></a>HdiRestoreClusterAltered

-   **설명**: *클러스터이름* 클러스터가 유지 관리 과정에서 삭제되었습니다. 클러스터를 다시 만드세요.
-   **완화**: 클러스터를 다시 만드세요.

### <span id="HeadNodeConfigNotFound"></span></a>HeadNodeConfigNotFound

-   **설명**: 클러스터 구성이 잘못되었습니다. 노드 크기에서 필수 헤드 노드 구성을 찾을 수 없습니다.
-   **완화**: 작업을 다시 시도하세요.

### <span id="HostedServiceCreationFailure"></span></a>HostedServiceCreationFailure

-   **설명**: 호스티드 서비스인 *호스티드서비스이름*을(를) 만들 수 없습니다. 요청을 다시 시도하세요.
-   **완화**: 요청을 다시 시도하세요.

### <span id="HostedServiceHasProductionDeployment"></span></a>HostedServiceHasProductionDeployment

-   **설명**: 호스티드 서비스인 *호스티드서비스이름*에 이미 프로덕션 배포가 있습니다. 호스티드 서비스에는 여러 프로덕션 배포를 포함할 수 없습니다. 다른 클러스터 이름으로 요청을 다시 시도하세요.
-   **완화**: 다른 클러스터 이름을 사용하여 요청을 다시 시도하세요.

### <span id="HostedServiceNotFound"></span></a>HostedServiceNotFound

-   **설명**: 클러스터의 호스티드 서비스인 *호스티드서비스이름*을(를) 찾을 수 없습니다.
-   **완화**: 클러스터가 오류 상태인 경우 클러스터를 삭제한 후 다시 시도하세요.

### <span id="HostedServiceWithNoDeployment"></span></a>HostedServiceWithNoDeployment

-   **설명**: 호스티드 서비스인 *호스티드서비스이름*에 연결된 배포가 없습니다.
-   **완화**: 클러스터가 오류 상태인 경우 클러스터를 삭제한 후 다시 시도하세요.

### <span id="InsufficientResourcesCores"></span></a>InsufficientResourcesCores

-   **설명**: SubscriptionId인 *구독Id*에 *클러스터이름* 클러스터를 만드는 데 필요한 코어가 남아 있지 않습니다. 필수: *필요한리소스*, 사용 가능: *사용가능한리소스*
-   **완화**: 구독에서 리소스를 해제하거나 구독에서 사용할 수 있는 리소스를 늘린 후 클러스터를 다시 만들어 보세요.

### <span id="InsufficientResourcesHostedServices"></span></a>InsufficientResourcesHostedServices

-   **설명**: 구독 ID인 *구독Id*에 *클러스터이름* 클러스터를 만들 새 HostedService에 대한 할당량이 없습니다.
-   **완화**: 구독에서 리소스를 해제하거나 구독에서 사용할 수 있는 리소스를 늘린 후 클러스터를 다시 만들어 보세요.

### <span id="InternalErrorRetryRequest"></span></a>InternalErrorRetryRequest

-   **설명**: 서버에 내부 오류가 발생했습니다. 요청을 다시 시도하세요.
-   **완화**: 요청을 다시 시도하세요.

### <span id="InvalidAzureStorageLocation"></span></a>InvalidAzureStorageLocation

-   **설명**: Azure 저장소 위치인 *데이터지역이름*이(가) 올바른 위치가 아닙니다. 지역이 올바른지 확인하고 요청을 다시 시도하세요.
-   **완화**: HDInsight를 지원하는 저장소 위치를 선택하고 클러스터가 공동 배치되었는지 확인한 후 작업을 다시 시도하세요.

### <span id="InvalidNodeSizeForDataNode"></span></a>InvalidNodeSizeForDataNode

-   **설명**: 데이터 노드의 VM 크기가 잘못되었습니다. '모든 데이터 노드에서 큰 VM' 크기만 지원됩니다.
-   **완화**: 데이터 노드에서 지원되는 노드 크기를 지정한 후 작업을 다시 시도하세요.

### <span id="InvalidNodeSizeForHeadNode"></span></a>InvalidNodeSizeForHeadNode

-   **설명**: 헤드 노드의 VM 크기가 잘못되었습니다. 헤드 노드에서는 '매우 큰 VM' 크기만 지원됩니다.
-   **완화**: 헤드 노드에서 지원되는 노드 크기를 지정하고 작업을 다시 시도하세요.

### <span id="InvalidRightsForDeploymentDeletion"></span></a>InvalidRightsForDeploymentDeletion

-   **설명**: 사용 중인 구독 ID인 *구독Id*에 *클러스터이름* 클러스터에 대한 삭제 작업을 실행할 수 있는 충분한 권한이 없습니다.
-   **완화**: 클러스터가 오류 상태인 경우 클러스터를 삭제한 후 다시 시도하세요.

### <span id="InvalidStorageAccountBlobContainerName"></span></a>InvalidStorageAccountBlobContainerName

-   **설명**: 외부 저장소 계정 Blob 컨테이너 이름인 *컨테이너이름*이(가) 잘못되었습니다. 이름이 문자로 시작되며 이름에 소문자, 숫자 및 대시만 포함되어 있는지 확인하세요.
-   **완화**: 올바른 저장소 계정 Blob 컨테이너 이름을 지정한 후 작업을 다시 시도하세요.

### <span id="InvalidStorageAccountConfigurationSecretKey"></span></a>InvalidStorageAccountConfigurationSecretKey

-   **설명**: 외부 저장소 계정인 *저장소계정이름*의 구성이 비밀 키 세부 정보를 설정하는 데 필요합니다.
-   **완화**: 저장소 계정에 올바른 비밀 키를 지정한 후 작업을 다시 시도하세요.

### <span id="InvalidVersionHeaderFormat"></span></a>InvalidVersionHeaderFormat

-   **설명**: 버전 헤더인 *버전헤더*이(가) yyyy-mm-dd의 올바른 형식이 아닙니다.
-   **완화**: 버전 헤더에 올바른 형식을 지정한 후 요청을 다시 시도하세요.

### <span id="MoreThanOneHeadNode"></span></a>MoreThanOneHeadNode

-   **설명**: 클러스터 구성이 잘못되었습니다. 헤드 노드 구성이 두 개 이상 검색되었습니다.
-   **완화**: 하나의 헤드 노드만 지정하도록 구성을 편집하세요.

### <span id="OperationTimedOutRetryRequest"></span></a>OperationTimedOutRetryRequest

-   **설명**: 허용된 시간 또는 가능한 최대 재시도 횟수 내에 작업을 완료할 수 없습니다. 요청을 다시 시도하세요.
-   **완화**: 요청을 다시 시도하세요.

### <span id="ParameterNullOrEmpty"></span></a>ParameterNullOrEmpty

-   **설명**: *매개변수이름* 매개 변수는 Null이거나 비워 둘 수 없습니다.
-   **완화**: 매개 변수에 올바른 값을 지정하세요.

### <span id="PreClusterCreationValidationFailure"></span></a>PreClusterCreationValidationFailure

-   **설명**: 하나 이상의 클러스터 만들기 요청 입력이 올바르지 않습니다. 입력 값이 올바른지 확인하고 요청을 다시 시도하세요.
-   **완화**: 입력 값이 올바른지 확인하고 요청을 다시 시도하세요.

### <span id="RegionCapabilityNotAvailable"></span></a>RegionCapabilityNotAvailable

-   **설명**: *지역이름* 지역 및 구독 ID인 *구독Id*에서 사용할 수 없는 지역 정보 값입니다.
-   **완화**: HDInsight 클러스터를 지원하는 지역을 지정하세요. 공개적으로 지원되는 지역은 동남아시아, 서유럽, 북유럽, 미국 동부 또는 미국 서버 중에서 만들어야 합니다.

### <span id="StorageAccountNotColocated"></span></a>StorageAccountNotColocated

-   **설명**: 저장소 계정인 *저장소계정이름*이(가) *현재지역이름* 지역에 있습니다. 지역은 클러스터 지역인 *클러스터지역이름*과(와) 동일해야 합니다.
-   **완화**: 클러스터가 있는 지역과 동일한 지역에서 저장소 계정을 지정하거나, 사용 중인 데이터가 저장소 계정에 이미 존재하는 경우 기존 저장소 계정과 동일한 지역에서 새 클러스터를 만드세요. 포털을 사용하는 경우 UI에서 이 문제에 대해 사전에 알려 줍니다.

### <span id="SubscriptionIdNotActive"></span></a>SubscriptionIdNotActive

-   **설명**: 지정된 구독 ID인 *구독Id*이(가) 활성이 아닙니다.
-   **완화**: 구독을 다시 활성화하거나 올바른 새 구독을 가져오세요.

### <span id="SubscriptionIdNotFound"></span></a>SubscriptionIdNotFound

-   **설명**: 구독 ID인 *구독Id*을(를) 찾을 수 없습니다.
-   **완화**: 구독 ID가 올바른지 확인한 후 작업을 다시 시도하세요.

### <span id="UnableToResolveDNS"></span></a>UnableToResolveDNS

-   **설명**: *DnsUrl* DNS를 확인할 수 없습니다. Blob 끝점에 정규화된 URL을 지정했는지 확인하세요.
-   **완화**: 올바른 Blob URL을 제공하세요. URL은 전체가 유효해야 하며, *http://*로 시작하고 *.com*으로 끝나야 합니다. 정규화된 URL은 일반적으로 manage.windowsazure.com 포털의 저장소 탭에서 찾아볼 수 있습니다.

### <span id="UnableToVerifyLocationOfResource"></span></a>UnableToVerifyLocationOfResource

-   **설명**: *DnsUrl* 리소스의 위치를 확인할 수 없습니다. Blob 끝점에 정규화된 URL을 지정했는지 확인하세요.
-   **완화**: 올바른 Blob URL을 제공하세요. URL은 전체가 유효해야 하며, *http://*로 시작하고 *.com*으로 끝나야 합니다. 정규화된 URL은 일반적으로 manage.windowsazure.com 포털의 저장소 탭에서 찾아볼 수 있습니다.

### <span id="VersionCapabilityNotAvailable"></span></a>VersionCapabilityNotAvailable

-   **설명**: *지정된버전* 버전 및 구독 ID인 *구독Id*에서 사용할 수 없는 버전 정보 값입니다.
-   **완화**: 사용할 수 있는 버전을 선택한 후 작업을 다시 시도하세요.

### <span id="VersionNotSupported"></span></a>VersionNotSupported

-   **설명**: *지정된버전* 버전은 지원되지 않습니다.
-   **완화**: 지원되는 버전을 선택한 후 작업을 다시 시도하세요.

### <span id="VersionNotSupportedInRegion"></span></a>VersionNotSupportedInRegion

-   **설명**: *지정된버전* 버전은 Azure 지역인 *지정된지역*에서 사용할 수 없습니다.
-   **완화**: 지정된 지역에서 지원되는 버전을 선택한 후 작업을 다시 시도하세요.

### <span id="WasbAccountConfigNotFound"></span></a>WasbAccountConfigNotFound

-   **설명**: 클러스터 구성이 잘못되었습니다. 필수 WASB 계정 구성을 외부 계정에서 찾을 수 없습니다.
-   **완화**: 계정이 존재하며 구성에서 올바르게 지정되었는지 확인한 후 작업을 다시 시도하세요.

## <span id="resources"></span></a>추가 디버깅 리소스

-   [Azure HDInsight SDK 문서][Azure HDInsight SDK 문서]

  [HDI.Debugging.ErrorMessages.Portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png
  [HDInsight 오류]: #hdinsight-error-messages
  [오류 설명 및 완화]: #discription-mitigation-errors
  [AtleastOneSqlMetastoreMustBeProvided]: #AtleastOneSqlMetastoreMustBeProvided
  [AzureRegionNotSupported]: #AzureRegionNotSupported
  [ClusterContainerRecordNotFound]: #ClusterContainerRecordNotFound
  [ClusterDnsNameInvalidReservedWord]: #ClusterDnsNameInvalidReservedWord
  [ClusterNameUnavailable]: #ClusterNameUnavailable
  [ClusterUserNameInvalid]: #ClusterUserNameInvalid
  [ClusterUserNameInvalidReservedWord]: #ClusterUserNameInvalidReservedWord
  [ContainerNameMisMatchWithDnsName]: #ContainerNameMisMatchWithDnsName
  [DataNodeDefinitionNotFound]: #DataNodeDefinitionNotFound
  [DeploymentDeletionFailure]: #DeploymentDeletionFailure
  [DnsMappingNotFound]: #DnsMappingNotFound
  [DuplicateClusterContainerRequest]: #DuplicateClusterContainerRequest
  [DuplicateClusterInHostedService]: #DuplicateClusterInHostedService
  [FailureToUpdateDeploymentStatus]: #FailureToUpdateDeploymentStatus
  [HdiRestoreClusterAltered]: #HdiRestoreClusterAltered
  [HeadNodeConfigNotFound]: #HeadNodeConfigNotFound
  [HostedServiceCreationFailure]: #HostedServiceCreationFailure
  [HostedServiceHasProductionDeployment]: #HostedServiceHasProductionDeployment
  [HostedServiceNotFound]: #HostedServiceNotFound
  [HostedServiceWithNoDeployment]: #HostedServiceWithNoDeployment
  [InsufficientResourcesCores]: #InsufficientResourcesCores
  [InsufficientResourcesHostedServices]: #InsufficientResourcesHostedServices
  [InternalErrorRetryRequest]: #InternalErrorRetryRequest
  [InvalidAzureStorageLocation]: #InvalidAzureStorageLocation
  [InvalidNodeSizeForDataNode]: #InvalidNodeSizeForDataNode
  [InvalidNodeSizeForHeadNode]: #InvalidNodeSizeForHeadNode
  [InvalidRightsForDeploymentDeletion]: #InvalidRightsForDeploymentDeletion
  [InvalidStorageAccountBlobContainerName]: #InvalidStorageAccountBlobContainerName
  [InvalidStorageAccountConfigurationSecretKey]: #InvalidStorageAccountConfigurationSecretKey
  [InvalidVersionHeaderFormat]: #InvalidVersionHeaderFormat
  [MoreThanOneHeadNode]: #MoreThanOneHeadNode
  [OperationTimedOutRetryRequest]: #OperationTimedOutRetryRequest
  [ParameterNullOrEmpty]: #ParameterNullOrEmpty
  [PreClusterCreationValidationFailure]: #PreClusterCreationValidationFailure
  [RegionCapabilityNotAvailable]: #RegionCapabilityNotAvailable
  [StorageAccountNotColocated]: #StorageAccountNotColocated
  [SubscriptionIdNotActive]: #SubscriptionIdNotActive
  [SubscriptionIdNotFound]: #SubscriptionIdNotFound
  [UnableToResolveDNS]: #UnableToResolveDNS
  [UnableToVerifyLocationOfResource]: #UnableToVerifyLocationOfResource
  [VersionCapabilityNotAvailable]: #VersionCapabilityNotAvailable
  [VersionNotSupported]: #VersionNotSupported
  [VersionNotSupportedInRegion]: #VersionNotSupportedInRegion
  [WasbAccountConfigNotFound]: #WasbAccountConfigNotFound
  [Azure HDInsight SDK 문서]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx
