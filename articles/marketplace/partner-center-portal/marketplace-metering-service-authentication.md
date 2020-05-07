---
title: Marketplace 계량 서비스 인증 전략 | Azure Marketplace
description: Azure Marketplace에서 지원 되는 계량 서비스 인증 전략입니다.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736202"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Marketplace 계량 서비스 인증 전략

Marketplace 계량 서비스는 두 가지 인증 전략을 지원 합니다.

* [Azure AD 보안 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Marketplace 계량 서비스를 사용 하 여 사용자 지정 측정기를 안전 하 게 제출 하기 위해 다양 한 인증 전략을 사용 하는 시기와 방법을 설명 합니다.

## <a name="using-the-azure-ad-security-token"></a>Azure AD 보안 토큰 사용

적용 가능한 제품 유형은 관리 되는 응용 프로그램 계획 유형을 사용 하는 SaaS 및 Azure 응용 프로그램입니다.  

미리 정의 된 고정 응용 프로그램 ID를 사용 하 여 사용자 지정 측정기를 제출 하 여 인증 합니다.

SaaS 제품의 경우 Azure AD는 유일 하 게 사용할 수 있는 옵션입니다.

관리 되는 응용 프로그램 계획이 있는 Azure 응용 프로그램의 경우 다음과 같은 경우에이 전략을 사용 하는 것을 고려해 야 합니다.

* 백 엔드 서비스와 통신 하는 메커니즘이 이미 있으며이 메커니즘을 확장 하 여 중앙 서비스에서 사용자 지정 측정기를 내보냅니다.
* 복잡 한 사용자 지정 측정기 논리를 사용할 수 있습니다.  관리 되는 응용 프로그램 리소스 대신 중앙 위치에서이 논리를 실행 합니다.

응용 프로그램을 등록 한 후에는 프로그래밍 방식으로 Azure AD 보안 토큰을 요청할 수 있습니다. 게시자는이 토큰을 사용 하 고이 토큰을 확인 하도록 요청 해야 합니다.

이러한 토큰에 대 한 자세한 내용은 [Azure Active Directory 액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)을 참조 하세요.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD 앱에 따라 토큰 가져오기

#### <a name="http-method"></a>HTTP 메서드

**올리기**

#### <a name="request-url"></a>*요청 URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI 매개 변수*

|  **매개 변수 이름** |  **필수**  |  **설명**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | 등록 된 Azure AD 응용 프로그램의 테 넌 트 ID입니다.   |
| | | |

#### <a name="request-header"></a>*요청 헤더*

|  **헤더 이름**    |  **필수**  |  **설명**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | 요청과 연결된 콘텐츠 형식입니다. 기본값은 `application/x-www-form-urlencoded`입니다.  |
| | | |

#### <a name="request-body"></a>*요청 본문*

|  **속성 이름**  |  **필수**  |  **설명**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | 권한 부여 유형입니다. 기본값은 `client_credentials`입니다. |
|  `Client_id`        |   True         | Azure AD 앱과 연결된 클라이언트/앱 식별자입니다.|
|  `client_secret`    |   True         | Azure AD 앱과 연결 된 암호입니다.  |
|  `Resource`         |   True         | 토큰이 요청된 대상 리소스입니다. 기본값은 `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`입니다.  |
| | | |

#### <a name="response"></a>*응답*

|  **Name**    |  **형식**  |  **설명**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | 요청이 성공 했습니다.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

샘플 응답 토큰:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Azure 관리 id 토큰 사용

적용 가능한 제품 유형은 관리 되는 응용 프로그램 계획 유형을 사용 하는 Azure 응용 프로그램입니다.

이 접근 방식을 사용 하면 배포 된 리소스 id를 인증 하 여 사용자 지정 측정기 사용 이벤트를 보낼 수 있습니다.  사용량을 내보내는 코드를 배포 경계 내에 포함할 수 있습니다.

>[!Note]
>게시자는 사용 현황을 내보내는 리소스가 잠겨 있는지 확인 해야 하므로 변조 되지 않습니다.

관리 되는 응용 프로그램은 Virtual Machines에서 Azure Functions까지 다른 유형의 리소스를 포함할 수 있습니다.  여러 서비스에 대해 관리 id를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 [Azure 리소스에 관리 id를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources)을 참조 하세요.

예를 들어 다음 단계에 따라 Windows VM을 사용 하 여 인증 합니다.

1. 다음 방법 중 하나를 사용 하 여 관리 Id를 구성 했는지 확인 합니다.
    * [Azure Portal UI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST (영문)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. 시스템 id, VM에 대 한 RDP를 사용 하`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`여 Marketplace 계량 서비스 응용 프로그램 ID ()에 대 한 액세스 토큰을 가져옵니다. PowerShell 콘솔을 열고 아래 명령을 실행 합니다.

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. 현재 리소스 그룹 ' ManagedBy ' 속성에서 관리 되는 앱 ID를 가져옵니다.

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace 계량 서비스는에 대 한 사용을 `resourceID`보고 하 `resourceUsageId` 고 관리 되는 응용 프로그램의 경우를 보고 해야 합니다.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. [Marketplace 계량 서비스 API](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847) 를 사용 하 여 사용 현황을 내보냅니다.

## <a name="next-steps"></a>다음 단계

* [Azure 애플리케이션 제품 만들기](./create-new-azure-apps-offer.md)