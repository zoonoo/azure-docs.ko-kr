---
title: Azure API Management에서 Service Fabric 백 엔드 설정 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure API Management에서 Service Fabric 서비스 백 엔드를 만드는 방법
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500631"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Azure Portal를 사용 하 여 API Management에 Service Fabric 백 엔드 설정

이 문서에서는 Azure Portal를 사용 하 여 [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) 서비스를 사용자 지정 API 백엔드로 구성 하는 방법을 보여 줍니다. 데모용으로, Service Fabric 백엔드로 기본 상태 비저장 ASP.NET Core 신뢰할 수 있는 서비스를 설정 하는 방법을 보여 줍니다.

백그라운드는 [API Management의 백 엔드](backends.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

Windows를 실행 하는 Service Fabric 클러스터에서 사용자 지정 백 엔드로 샘플 서비스를 구성 하기 위한 필수 조건:

* **Windows 개발 환경** - [Visual Studio 2019](https://www.visualstudio.com) 및 **Azure 개발**, **ASP.NET 및 웹 개발**, **.net Core 플랫폼 간 개발** 워크 로드를 설치 합니다. 그런 후 [.NET 개발 환경](../service-fabric/service-fabric-get-started.md)을 설정합니다.

* **Service Fabric 클러스터** - [자습서: Windows를 실행 하는 Service Fabric 클러스터를 Azure 가상 네트워크에 배포를](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md)참조 하세요. 기존 x.509 인증서를 사용 하 여 클러스터를 만들거나 테스트 목적으로 자체 서명 된 새 인증서를 만들 수 있습니다. 클러스터가 가상 네트워크에 만들어집니다.

* **샘플 Service Fabric 앱** - [Azure에서 Service Fabric와 API Management 통합](../service-fabric/service-fabric-tutorial-deploy-api-management.md)에 설명 된 대로 웹 API 앱을 만들고 Service Fabric 클러스터에 배포 합니다.

    이러한 단계에서는 기본 웹 API 프로젝트 템플릿을 사용 하 여 신뢰할 수 있는 기본 상태 비저장 ASP.NET Core 서비스를 만듭니다. 나중에 Azure API Management를 통해이 서비스에 대 한 HTTP 끝점을 노출 합니다.

    응용 프로그램 이름 (예:)을 기록해 둡니다 `fabric:/myApplication/myService` . 

* **API Management 인스턴스** - **프리미엄** 또는  **개발자** 계층의 기존 또는 새 API Management 인스턴스와 Service Fabric 클러스터와 동일한 지역에 있습니다. 필요한 경우 [API Management 인스턴스를 만듭니다](get-started-create-service-instance.md).

* **가상 네트워크** -Service Fabric 클러스터에 대해 만든 가상 네트워크에 API Management 인스턴스를 추가 합니다. API Management 가상 네트워크에서 전용 서브넷이 필요 합니다.

  API Management 인스턴스에 대 한 가상 네트워크 연결을 사용 하도록 설정 하 [는 단계는 가상 네트워크에서 Azure API Management를 사용 하는 방법](api-management-using-with-vnet.md)을 참조 하세요.

## <a name="create-backend---portal"></a>백 엔드 만들기-포털

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>API Management에 Service Fabric 클러스터 인증서 추가

Service Fabric 클러스터 인증서는 클러스터와 연결 된 Azure key vault에 저장 되 고 관리 됩니다. 이 인증서를 API Management 인스턴스에 클라이언트 인증서로 추가 합니다.

API Management 인스턴스에 인증서를 추가 하 [는 단계는 Azure API Management에서 클라이언트 인증서 인증을 사용 하 여 백 엔드 서비스를 보호 하는 방법](api-management-howto-mutual-certificates.md)을 참조 하세요. 

> [!NOTE]   
> 키 자격 증명 모음 인증서를 참조 하 여 API Management에 인증서를 추가 하는 것이 좋습니다. 

### <a name="add-service-fabric-backend"></a>Service Fabric 백 엔드 추가

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **Api** 아래에서 **백 엔드**  >  **+ 추가** 를 선택 합니다.
1. 백 엔드 이름 및 설명 (선택 사항)을 입력 합니다.
1. **형식** 에서 **Service Fabric** 를 선택 합니다.
1. **런타임 URL** 에 요청을 전달 API Management는 Service Fabric 백 엔드 서비스의 이름을 입력 합니다. 예: `fabric:/myApplication/myService`. 
1. **최대 파티션 확인 다시 시도** 횟수에서 0에서 10 사이의 숫자를 입력 합니다.
1. Service Fabric 클러스터의 관리 끝점을 입력 합니다. 이 끝점은 포트에서 클러스터의 URL `19080` (예:)입니다 `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. **클라이언트 인증서** 의 이전 섹션에서 API Management 인스턴스에 추가한 Service Fabric 클러스터 인증서를 선택 합니다.
1. **관리 끝점 권한 부여 방법** 에서 TLS 통신용 Service Fabric 클러스터 관리 서비스에 사용 되는 인증서의 지문 또는 서버 X509 이름을 입력 합니다.
1. **인증서 체인 유효성 검사** 및 **인증서 이름 유효성 검사** 설정을 사용 하도록 설정 합니다.
1. **권한 부여 자격 증명** 에서 필요한 경우 자격 증명을 제공 하 여 Service Fabric에서 구성 된 백 엔드 서비스에 연결 합니다. 이 시나리오에 사용 된 샘플 앱의 경우 권한 부여 자격 증명이 필요 하지 않습니다.
1. **만들기** 를 선택합니다.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Service fabric 백 엔드 만들기":::

## <a name="use-the-backend"></a>백 엔드 사용

사용자 지정 백 엔드를 사용 하려면 정책을 사용 하 여 참조 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 합니다. 이 정책은 들어오는 API 요청의 기본 백 엔드 서비스 기준 URL을 지정 된 백 엔드로 변환 합니다 .이 경우에는 Service Fabric 백 엔드입니다. 

이 `set-backend-service` 정책은 기존 api와 함께 사용 하 여 들어오는 요청을 api 설정에 지정 된 것과 다른 백 엔드로 변환할 수 있습니다. 이 문서의 데모용으로 테스트 API를 만들고 API 요청을 Service Fabric 백 엔드에 전달 하도록 정책을 설정 합니다. 

### <a name="create-api"></a>API 만들기

[Api를 수동으로 추가](add-api-manually.md) 의 단계에 따라 빈 api를 만듭니다.

* API 설정에서 **웹 서비스 URL** 을 비워 둡니다.
* *패브릭* 과 같은 **API URL 접미사** 를 추가 합니다.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="빈 API 만들기":::

### <a name="add-get-operation-to-the-api"></a>API에 GET 작업 추가

[Service Fabric 백 엔드 서비스 배포](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)에 표시 된 것 처럼 Service Fabric 클러스터에 배포 된 샘플 ASP.NET Core 서비스는 URL 경로에서 단일 HTTP GET 작업을 지원 합니다 `/api/values` .

해당 경로에 대 한 기본 응답은 두 문자열의 JSON 배열입니다.

```json
["value1", "value2"]
```

클러스터와 API Management의 통합을 테스트 하려면 해당 GET 작업을 API의 경로에 추가 합니다 `/api/values` .

1. 이전 단계에서 만든 API를 선택합니다.
1. **+ 작업 추가** 를 선택합니다.
1. **프런트 엔드** 창에서 다음 값을 입력 하 고 **저장** 을 선택 합니다.

     | 설정             | 값                             | 
    |---------------------|-----------------------------------|
    | **표시 이름**    | *테스트 백 엔드*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="API에 GET 작업 추가":::

### <a name="configure-set-backend-policy"></a>`set-backend`정책 구성

[`set-backend-service`](api-management-transformation-policies.md#SetBackendService)테스트 API에 정책을 추가 합니다.

1. **디자인** 탭의 **인바운드 처리** 섹션에서 코드 편집기 () 아이콘을 선택 합니다 **</>** . 
1. **&lt; 인바운드 &gt;** 요소 내에 커서 배치
1. 다음 정책 문을 추가 합니다. 에서 `backend-id` Service Fabric 백 엔드의 이름을 대체 합니다.

   는 `sf-resolve-condition` 클러스터 파티션이 확인 되지 않는 경우 재시도 조건입니다. 백 엔드를 구성할 때 다시 시도 횟수가 설정 되었습니다.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. **저장** 을 선택합니다.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="설정-백 엔드-서비스 정책 구성":::

### <a name="test-backend-api"></a>테스트 백 엔드 API

1. **테스트** 탭에서 이전 섹션에서 만든 **가져오기** 작업을 선택 합니다.
1. **보내기** 를 선택합니다.

올바르게 구성 된 경우 HTTP 응답은 HTTP 성공 코드를 표시 하 고 백 엔드 Service Fabric 서비스에서 반환 된 JSON을 표시 합니다.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="테스트 Service Fabric 백 엔드":::

## <a name="next-steps"></a>다음 단계

* 백 엔드에 요청을 전달 하는 [정책을 구성](api-management-advanced-policies.md) 하는 방법에 대해 알아봅니다.
* 백 엔드는 API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)또는 [Azure Resource Manager 템플릿](../service-fabric/service-fabric-tutorial-deploy-api-management.md) 을 사용 하 여 구성할 수도 있습니다.

