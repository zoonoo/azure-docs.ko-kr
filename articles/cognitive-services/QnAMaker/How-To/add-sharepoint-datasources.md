---
title: Sharepoint 파일-QnA Maker
titleSuffix: Azure Cognitive Services
description: Active Directory를 사용 하 여 보호할 수 있는 질문과 대답을 사용 하 여 기술 자료를 보강 하 여 기술 자료로 보안 된 Sharepoint 데이터 소스를 추가 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 3e5aa1cc78efeb6e8158155b5e0676c8a63cf6e6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447544"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>기술 자료에 보안된 Sharepoint 데이터 소스 추가

Active Directory를 사용 하 여 보호할 수 있는 질문과 대답을 사용 하 여 기술 자료를 보강 하 여 기술 자료로 보안 된 Sharepoint 데이터 소스를 추가 합니다. 

기술 자료에 QnA Maker 관리자로 보안 된 Sharepoint 문서를 추가 하는 경우 QnA Maker에 대 한 Active Directory 권한을 요청 해야 합니다. 이 권한이 부여 되는 Active Directory 관리자에서 QnA Maker를 Sharepoint에 대 한 액세스에 대 한 후에 다시 지정할 수 없습니다. 각 후속 문서 추가 기술 자료를 동일한 Sharepoint 리소스에 있는 경우 권한 부여를 해야 합니다. 

QnA Maker 자료 manager Active Directory 관리자를 사용 하는 경우에이 프로세스를 완료 하려면 Active Directory 관리자와 통신 해야 합니다.

## <a name="add-supported-file-types-to-knowledge-base"></a>기술 자료에 지원 되는 파일 형식 추가

QnA Maker를 모두 지원를 추가할 수 있습니다 [파일 형식](../Concepts/data-sources-supported.md) Sharepoint 서버에 기술 자료에서 합니다. 권한을 부여 해야 할 수 있습니다 [권한을](#permissions) 파일 리소스 보호 되는 경우.

1. Sharepoint 서버에서 파일의 줄임표 메뉴 선택 `...`합니다.
1. 파일의 URL을 복사 합니다.

    ![다음 URL을 복사 하는 파일의 줄임표 메뉴를 선택 하 여 Sharepoint 파일 URL을 가져옵니다.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. QnA Maker 포털에서에 **설정** 페이지에서 [URL 추가](edit-knowledge-base.md#add-datasource) 기술 자료에 합니다. 

### <a name="images-with-sharepoint-files"></a>Sharepoint 파일을 사용 하 여 이미지

파일 이미지를 포함 하는 경우 해당 되지 추출 됩니다. 파일은 QnA 쌍으로 추출한 후 QnA Maker 포털에서 이미지를 추가할 수 있습니다.

Markdown 구문 사용 하 여 이미지를 추가 합니다. 

```markdown
![Explanation or description of image](URL of public image)
```

대괄호 안에 있는 텍스트 `[]`, 이미지에 설명 합니다. 괄호에 있는 URL `()`, 이미지에 대 한 직접 링크입니다. 

QnA Maker 포털에서 대화형 테스트 패널에서 QnA 쌍을 테스트 하는 경우 이미지를 markdown 텍스트 대신 표시 됩니다. 이미지의 유효성을 검사 하는이 클라이언트 응용 프로그램에서 공개적으로 검색할 수 있습니다.

## <a name="permissions"></a>권한

사용 권한을 부여할 Sharepoint 서버에서 보안된 파일을 기술 자료에 추가 되 면 발생 합니다. Sharepoint 설정 방법에 따라 및 필요할 수 있습니다이 파일을 추가 하는 사용자의 권한:

* -추가 단계 없이 파일을 추가 하는 사용자에 필요한 모든 권한이 있습니다.
* 둘 다가 단계를 수행 [기술 자료 manager](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) 하 고 [Active Directory 관리자](#active-directory-manager-grant-file-read-access-to-qna-maker)합니다.

아래에 나열 된 단계를 참조 하세요. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>기술 자료 관리자: QnA Maker 포털에서 Sharepoint 데이터 원본 추가

경우는 **QnA Maker manager** Active Directory 관리자를 완료 해야 하는 사용 권한에 대 한 요청을 기술 자료, 기술 자료 관리자 시작에 보안 된 Sharepoint 문서를 추가 합니다.

요청을 Active Directory 계정을 인증 하려면 팝업을 사용 하 여 시작 합니다. 

![사용자 계정 인증](../media/add-sharepoint-datasources/authenticate-user-account.png)

QnA Maker 관리자 계정 선택, Active Directory 관리자가 QnA Maker Sharepoint 리소스에 대 한 앱 (없습니다 QnA Maker 관리자) 액세스를 허용 해야 하는 공지를 받게 됩니다. Active Directory 관리자를 사용 하는 모든 Sharepoint 리소스에 있지만 해당 리소스의 모든 문서에 대 한이 작업을 수행 해야 합니다. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory 관리자: QnA Maker에 파일 읽기 액세스 부여

Active Directory 관리자 (하지 QnA Maker 관리자)가 QnA Maker를 선택 하 여 Sharepoint 리소스에 액세스 하 게 액세스 권한을 부여 해야 [이 링크](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) 파일 읽기 있어야 QnA Maker 포털 Sharepoint 엔터프라이즈 앱에 권한을 부여 하려면 사용 권한입니다. 

![Azure Active Directory 관리자 권한을 대화형으로 부여](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에서 액세스 권한 부여

1. Active Directory 관리자는 Azure portal에 로그인 하 고 엽니다  **[엔터프라이즈 응용 프로그램](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** 합니다. 

1. 검색할 `QnAMakerPortalSharepoint` QnA Maker 앱을 선택 합니다. 

    [![엔터프라이즈 앱 목록의 QnAMakerPortalSharepoint 검색](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 아래 **보안**로 이동 하세요 **권한을**합니다. 선택 **조직에 대 한 관리자 동의 부여**합니다. 

    [![Active Directory 관리자에 대 한 인증 된 사용자를 선택 합니다.](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Active Directory에 대 한 권한을 부여할 수 있는 권한이 있는 로그온 계정을 선택 합니다. 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에서 공동 작업](collaborate-knowledge-base.md)
