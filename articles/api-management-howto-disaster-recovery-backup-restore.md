<properties pageTitle="How to implement disaster recovery using service backup and restore in Azure API Management" metaKeywords="" description="Learn how to use backup and restore to perform disaster recovery in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to implement disaster recovery using service backup and restore in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Azure API 관리에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법

Azure API 관리를 통해 API를 게시 및 관리하면 기존에는 직접 디자인, 구현 및 관리해야 했던 대부분의 내결함성 및 인프라 기능을 활용할 수 있습니다. Azure 플랫폼은 매우 적은 비용으로 상당한 잠재적 오류를 완화합니다.

API 관리 서비스를 호스트하는 지역에 영향을 주는 가용성 문제로부터 서비스를 복구하려면 언제든지 다른 지역에서 서비스를 다시 구축할 준비가 되어 있어야 합니다. 가용성 목표와 복구 시간 목표에 따라서는 하나 이상의 지역에서 백업 서비스를 예약하고 해당 구성과 콘텐츠를 활성 서비스와 동기화 상태로 유지 관리할 수 있습니다. 서비스 백업 및 복원 기능은 재해 복구 전략을 구현하는 데 필요한 구성 요소를 제공합니다.

서비스 관리 REST API를 통해 서비스 백업 및 복원 기능을 사용할 수 있습니다. API 액세스 권한을 얻는 방법에 대한 지침은 [Azure 리소스 관리자 요청 인증][Azure 리소스 관리자 요청 인증]을 참조하세요.

## 항목 내용

-   [API 관리 서비스 백업][API 관리 서비스 백업]
-   [API 관리 서비스 복원][API 관리 서비스 복원]

## <a name="step1"> </a>API 관리 서비스 백업

API 관리 서비스를 백업하려면 다음 HTTP 요청을 실행합니다.

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

설명:

-   `subscriptionId` - 백업할 API 관리 서비스를 포함하는 구독의 ID입니다.
-   `resourceGroupName` - 'Api-Default-{service-region}' 형식의 문자열입니다. 여기서 `service-region`은 백업할 API 관리 서비스가 호스트되는 Azure 지역(예: `North-Central-US`)을 식별합니다.
-   `serviceName` - 백업을 만드는 API 관리 서비스를 만들 때 지정하는 이름입니다.
-   `api-version` - 이 항목은 `2014-02-14`로 바꿉니다.

요청 본문에서 대상 Azure 저장소 계정, 액세스 키, Blob 컨테이너 이름 및 백업 이름을 지정합니다.

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

`Content-Type` 요청 헤더의 값을 `application\json`으로 설정합니다.

백업은 오랫동안 실행되는 작업으로, 완료되려면 몇 분이 걸릴 수 있습니다. 요청이 정상적으로 실행되어 백업 프로세스가 시작되면 `Location` 헤더가 포함된 `202 Accepted` 응답 상태 코드를 받게 됩니다. `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 백업이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK`이면 백업 작업이 정상적으로 완료된 것입니다.

**참고**:

-   요청 본문에 지정된 **Container**가 **있어야 합니다**.
-   백업이 진행 중인 동안에는 계층 업그레이드/다운그레이드, 도메인 이름 변경 등의 **서비스 관리 작업을 수행하면 안 됩니다**.
-   백업 복원은 생성 시점부터 **7일 동안만 보장**됩니다.
-   분석 보고서를 만드는 데 사용되는 **사용 현황 데이터**는 백업에 **포함되지 않습니다**. [Azure API 관리 REST API][Azure API 관리 REST API]를 사용하여 분석 보고서를 주기적으로 검색한 다음 안전하게 보관하세요.
-   서비스 백업을 수행하는 빈도는 복구 지점 목표에 영향을 줍니다. 영향을 최소화하려면 정기 백업을 구현함과 동시에 API 관리 서비스에 대한 중요 변경을 수행한 후 요청 시 백업도 수행하는 것이 좋습니다.
-   백업 작업이 진행되는 동안 API, 정책, 개발자 포털 모양 등의 서비스 구성을 **변경**하는 경우 **해당 내용이 백업에 포함되지 않아 손실될 수 있습니다**.

## <a name="step2"> </a>API 관리 서비스 복원

이전에 만든 백업에서 API 관리 서비스를 복원하려면 다음 HTTP 요청을 실행합니다.

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

설명:

-   `subscriptionId` - 백업을 복원할 API 관리 서비스를 포함하는 구독의 ID입니다.
-   `resourceGroupName` - 'Api-Default-{service-region}' 형식의 문자열입니다. 여기서 `service-region`은 백업을 복원할 API 관리 서비스가 호스트되는 Azure 지역(예: `North-Central-US`)을 식별합니다.
-   `serviceName` - 백업을 복원할 API 관리 서비스를 만들 때 지정한 이름입니다.
-   `api-version` - 이 항목은 `2014-02-14`로 바꿉니다.

요청 본문에서 백업 파일 위치(Azure 저장소 계정, 액세스 키, Blob 컨테이너 이름 및 백업 이름)를 지정합니다.

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

`Content-Type` 요청 헤더의 값을 `application\json`으로 설정합니다.

복원은 오랫동안 실행되는 작업으로, 완료되려면 30분 이상이 걸릴 수 있습니다. 요청이 정상적으로 실행되어 복원 프로세스가 시작되면 `Location` 헤더가 포함된 `202 Accepted` 응답 상태 코드를 받게 됩니다. `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 복원이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK`이면 복원 작업이 정상적으로 완료된 것입니다.

**참고**:

-   백업을 복원할 서비스의 **계층**은 복원하려는 백업된 서비스의 계층과 **일치**해야 합니다.
-   복원 작업이 진행되는 동안 API, 정책, 개발자 포털 모양 등의 서비스 구성에 적용된 **변경 내용**을 **덮어쓸 수 있습니다**.

  [Azure 리소스 관리자 요청 인증]: http://msdn.microsoft.com/en-us/library/dn790557.aspx
  [API 관리 서비스 백업]: #step1
  [API 관리 서비스 복원]: #step2
  [Azure API 관리 REST API]: http://msdn.microsoft.com/en-us/library/azure/dn781421.aspx
