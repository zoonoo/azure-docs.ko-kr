---
title: 공유점 파일 - QnA 메이커
description: 기술 자료에 보안 SharePoint 데이터 원본을 추가하여 Active Directory로 보호할 수 있는 질문과 답변으로 기술 자료를 보강합니다.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294872"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>기술 자료에 보안 SharePoint 데이터 원본 추가

보안 클라우드 기반 SharePoint 데이터 원본을 기술 자료에 추가하여 Active Directory로 보호할 수 있는 질문과 답변으로 기술 기반을 보강합니다.

QnA Maker 관리자로서 기술 자료에 보안 SharePoint 문서를 추가할 때 QnA Maker에 대한 Active Directory 권한을 요청해야 합니다. 이 권한이 활성 디렉터리 관리자에서 SharePoint에 액세스하기 위해 QnA Maker에 제공되면 다시 부여할 필요가 없습니다. 기술 자료에 추가된 각 후속 문서는 동일한 SharePoint 리소스에 있는 경우 권한 부여가 필요하지 않습니다.

QnA Maker 기술 자료 관리자가 Active Directory 관리자가 아닌 경우 이 프로세스를 완료하려면 Active Directory 관리자와 통신해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 클라우드 기반 SharePoint - QnA Maker는 사용 권한에 Microsoft 그래프를 사용합니다. SharePoint가 온-프레미스인 경우 Microsoft 그래프에서 권한을 확인할 수 없으므로 SharePoint에서 추출할 수 없습니다.
* URL 형식 - QnA Maker는 공유를 위해 생성되고 형식이 지정된 SharePoint URL만 지원합니다.`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>지원되는 파일 형식을 기술 자료에 추가

SharePoint 사이트에서 기술 자료에 QnA Maker지원 [파일 형식을](../Concepts/content-types.md) 모두 추가할 수 있습니다. 파일 [리소스가](#permissions) 보호된 경우 사용 권한을 부여해야 할 수 있습니다.

1. SharePoint 사이트가 있는 라이브러리에서 파일의 타원 메뉴를 `...`선택합니다.
1. 파일의 URL을 복사합니다.

   ![파일의 타원 메뉴를 선택한 다음 URL을 복사하여 SharePoint 파일 URL을 가져옵니다.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. QnA Maker 포털에서 **설정** 페이지에서 기술 자료에 [URL을 추가합니다.](manage-knowledge-bases.md#edit-knowledge-base)

### <a name="images-with-sharepoint-files"></a>공유점 파일이 있는 이미지

파일에 이미지가 포함된 경우 해당 파일은 추출되지 않습니다. 파일이 QnA 쌍으로 추출된 후 QnA Maker 포털에서 이미지를 추가할 수 있습니다.

다음 마크다운 구문으로 이미지를 추가합니다.

```markdown
![Explanation or description of image](URL of public image)
```

대괄호에 있는 텍스트는 `[]`이미지를 설명합니다. 괄호 의 URL은 `()`이미지에 대한 직접 링크입니다.

대화형 테스트 패널에서 QnA 쌍을 테스트하면 QnA Maker 포털에서 마크다운 텍스트 대신 이미지가 표시됩니다. 이렇게 하면 클라이언트 응용 프로그램에서 이미지를 공개적으로 검색할 수 있습니다.

## <a name="permissions"></a>사용 권한

권한 부여는 SharePoint 서버의 보안 파일이 기술 자료에 추가될 때 발생합니다. SharePoint를 설정하는 방법과 파일을 추가하는 사람의 권한에 따라 다음이 필요할 수 있습니다.

* 추가 단계 없음 - 파일을 추가하는 사람에게 필요한 모든 권한이 있습니다.
* [기술 자료 관리자와](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) [Active Directory 관리자모두의](#active-directory-manager-grant-file-read-access-to-qna-maker)단계입니다.

아래 단계를 참조하십시오.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>기술 자료 관리자: QnA Maker 포털에 SharePoint 데이터 원본 추가

**QnA Maker 관리자가** 기술 자료에 보안 SharePoint 문서를 추가하면 기술 자료 관리자는 Active Directory 관리자가 완료해야 하는 권한 요청을 시작합니다.

요청은 Active Directory 계정에 인증하는 팝업으로 시작됩니다.

![사용자 계정 인증](../media/add-sharepoint-datasources/authenticate-user-account.png)

QnA Maker 관리자가 계정을 선택하면 Azure Active Directory 관리자는 QnA Maker 앱(QnA Maker 관리자가 아님)이 SharePoint 리소스에 액세스할 수 있도록 허용해야 한다는 알림을 받게 됩니다. Azure Active Directory 관리자는 모든 SharePoint 리소스에 대해 이 작업을 수행해야 하지만 해당 리소스의 모든 문서는 수행할 수 없습니다.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>활성 디렉터리 관리자: QnA Maker에 대한 파일 읽기 액세스 권한 부여

QnA Maker 관리자가 아닌 Active Directory 관리자)는 [이 링크를](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) 선택하여 QnA Maker에 액세스하여 파일 읽기 권한을 부여해야 합니다.

![Azure Active Directory 관리자가 대화식으로 권한을 부여합니다.](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

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

1. Active Directory 관리자는 Azure 포털에 로그인하고 **[엔터프라이즈 응용 프로그램을 엽니다.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**

1. `QnAMakerPortalSharePoint` QnA 메이커 앱을 선택하여 검색합니다.

    [![엔터프라이즈 앱 목록에서 QnAMakerPortalSharePoint 검색](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. **보안에서** **권한으로 이동합니다.** **조직에 대한 관리자 동의를**선택합니다.

    [![활성 디렉터리 관리자에 대해 인증된 사용자 선택](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Active Directory에 대한 권한을 부여할 수 있는 권한이 있는 사인온 계정을 선택합니다.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

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

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에 대한 공동 작업](collaborate-knowledge-base.md)
