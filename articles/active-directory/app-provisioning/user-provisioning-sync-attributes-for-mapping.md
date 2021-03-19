---
title: 매핑을 위해 Azure AD에 특성 동기화
description: SaaS 앱에 대 한 사용자 프로비저닝을 구성할 때 디렉터리 확장 기능을 사용 하 여 기본적으로 동기화 되지 않은 원본 특성을 추가 합니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579436"
---
# <a name="syncing-extension-attributes-attributes"></a>확장 특성 동기화 특성

사용자 프로 비전을 위해 특성 매핑을 사용자 지정할 때 매핑하려는 특성이 **원본 특성** 목록에 표시 되지 않을 수 있습니다. 이 문서에서는 온-프레미스 Active Directory (AD)에서 Azure Active Directory (Azure AD)로 동기화 하거나 클라우드 전용 사용자에 대 한 Azure AD에서 확장 특성을 만들어 누락 된 특성을 추가 하는 방법을 보여 줍니다. 

Azure ad에서 SaaS 앱으로 사용자 계정을 프로 비전 할 때 사용자 프로필을 만드는 데 필요한 모든 데이터를 azure AD에 포함 해야 합니다. 데이터를 사용할 수 있도록 하기 위해 온-프레미스 AD에서 Azure AD로 특성을 동기화 해야 하는 경우도 있습니다. Azure AD Connect는 특정 특성을 Azure AD에 자동으로 동기화 하지만 모든 특성은 자동으로 동기화 하지 않습니다. 또한 기본적으로 동기화 되는 일부 특성 (예: SAMAccountName)은 Azure AD Graph API를 사용 하 여 노출 되지 않을 수 있습니다. 이러한 경우 Azure AD Connect directory 확장 기능을 사용 하 여 특성을 Azure AD와 동기화 할 수 있습니다. 이렇게 하면 특성이 Azure AD Graph API 및 Azure AD 프로 비전 서비스에 표시 됩니다. 프로 비전에 필요한 데이터가 Active Directory 이지만 위에서 설명한 이유로 프로 비전에 사용할 수 없는 경우 Azure AD Connect를 사용 하 여 확장 특성을 만들 수 있습니다. 

대부분의 사용자는 Active Directory에서 동기화 되는 하이브리드 사용자 일 수 있지만 Azure AD Connect를 사용 하지 않고 클라우드 전용 사용자에 대 한 확장을 만들 수도 있습니다. PowerShell 또는 Microsoft Graph를 사용 하 여 클라우드 전용 사용자의 스키마를 확장할 수 있습니다. 

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Azure AD Connect를 사용 하 여 확장 특성 만들기

1. Azure AD Connect 마법사를 열고 작업을 선택한 다음 **동기화 옵션 사용자 지정** 을 선택 합니다.

   ![Azure Active Directory Connect 마법사 추가 작업 페이지](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD 전역 관리자 권한으로 로그인 합니다. 

3. **선택적 기능** 페이지에서 **디렉터리 확장 특성 동기화** 를 선택 합니다.
 
   ![Azure Active Directory Connect 마법사 옵션 기능 페이지](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Azure AD로 확장 하려는 특성을 선택 합니다.
   > [!NOTE]
   > **사용 가능한 특성** 에서 검색은 대/소문자를 구분 합니다.

   !["디렉터리 확장" 선택 페이지를 보여 주는 스크린샷](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect 마법사를 완료 하 고 전체 동기화 주기를 실행 하도록 허용 합니다. 주기가 완료 되 면 스키마가 확장 되 고 새 값이 온-프레미스 AD와 Azure AD 간에 동기화 됩니다.
 
6. Azure Portal에서 [사용자 특성 매핑을 편집](customize-application-attributes.md)하는 동안 **원본 특성** 목록에는 이제 추가 된 특성이 형식으로 포함 됩니다 `<attributename> (extension_<appID>_<attributename>)` . 특성을 선택 하 고 프로 비전을 위한 대상 응용 프로그램에 매핑합니다.

   ![Azure Active Directory Connect 마법사 디렉터리 확장 선택 페이지](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> **Managedby** 또는 **DN/DistinguishedName** 등의 온-프레미스 AD에서 참조 특성을 프로 비전 하는 기능은 현재 지원 되지 않습니다. [사용자 음성](https://feedback.azure.com/forums/169401-azure-active-directory)에서이 기능을 요청할 수 있습니다. 

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>클라우드 전용 사용자에 대 한 확장 특성 만들기
고객은 Microsoft Graph 및 PowerShell을 사용 하 여 사용자 스키마를 확장할 수 있습니다. 이러한 확장 특성은 대부분의 경우 자동으로 검색 되지만 서비스 사용자가 1000 이상인 고객은 원본 특성 목록에 누락 된 확장을 찾을 수 있습니다. 아래 단계를 사용 하 여 만든 특성이 원본 특성 목록에 자동으로 표시 되지 않는 경우 그래프를 사용 하 여 확장 특성이 성공적으로 만들어졌는지 확인 한 다음 [수동으로](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes)스키마에 추가 하십시오. 아래 그래프 요청을 만들 때 자세히 알아보기를 클릭 하 여 요청을 수행 하는 데 필요한 권한을 확인 하세요. [그래프 탐색기](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) 를 사용 하 여 요청을 수행할 수 있습니다. 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Microsoft Graph를 사용 하 여 클라우드 전용 사용자에 확장 특성 만들기
응용 프로그램을 사용 하 여 사용자의 스키마를 확장 해야 합니다. 사용자 스키마를 확장 하는 데 사용할 응용 프로그램의 id를 식별 하기 위해 테 넌 트의 앱을 나열 합니다. [자세한 정보](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

확장 특성을 만듭니다. 다음 **id** 속성을 이전 단계에서 검색 된 **id** 로 바꿉니다. "AppId"를 사용 하는 것이 아니라 **"id"** 특성을 사용 해야 합니다. [자세한 정보](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

이전 요청에서 "extension_appID_extensionName" 형식의 확장 특성을 만들었습니다. 확장 특성으로 사용자를 업데이트 합니다. [자세한 정보](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
사용자가 특성을 성공적으로 업데이트 했는지 확인 합니다. [자세한 정보](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>PowerShell을 사용 하 여 클라우드 전용 사용자에 확장 특성 만들기
PowerShell을 사용 하 여 사용자 지정 확장을 만들고 사용자에 게 값을 할당 합니다. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>다음 단계

* [프로 비전 범위에 있는 사용자 정의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
