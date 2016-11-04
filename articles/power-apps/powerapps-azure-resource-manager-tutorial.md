---
title: '자습서: PowerApps 및 논리 흐름에서 Azure Resource Manager를 사용하여 사용자 지정 API 만들기 | Microsoft Docs'
description: PowerApps 및 논리 흐름에서 사용자 지정 API를 만드는 방법에 대한 Azure Resource Manager 자습서
services: ''
suite: powerapps
documentationcenter: ''
author: sunaysv
manager: erikre
editor: ''

ms.service: powerapps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2016
ms.author: mandia

---
# 자습서: PowerApps 및 논리 흐름에 대한 사용자 지정 AAD로 보호된 ARM API 만들기
이 자습서에서는 [ARM API][6]를 설명하는 Swagger 파일을 등록한 다음 PowerApps에서 사용자 지정 API에 연결하는 데 필요한 단계를 안내합니다.

> [!IMPORTANT]
> 이 항목은 [자습서: PowerApps 및 논리 흐름에 대한 사용자 지정 AAD로 보호된 ARM API 만들기](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)의 powerapps.microsoft.com으로 이동되었습니다. 최신 버전에 대한 PowerApps로 이동하세요. 이 Azure 링크가 보관됩니다.
> 
> 

## 시작에 필요한 항목
* Azure 구독
* PowerApps 계정

## Azure Active Directory에서 인증 사용
먼저 ARM API 끝점을 호출할 때 인증을 수행하는 AAD(Azure Active Directory) 응용 프로그램을 만들어야 합니다.

1. AAD 응용 프로그램을 만들려면 [Azure 구독][7]에 로그인한 후 **Active Directory**로 이동합니다. ![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "Azure AAD")
2. 이 페이지에서 AAD 응용 프로그램을 만들려는 디렉터리를 선택합니다. 디렉터리 선택한 후 **응용 프로그램** 탭으로 이동하여 **추가**를 선택합니다. ![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Azure 응용 프로그램")
3. 응용 프로그램 이름을 입력하고 **웹 응용 프로그램 및/또는 Web API**를 선택한 후 **다음**을 선택합니다. ![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "새 응용 프로그램")
4. **로그온 URL**에 *http://login.windows.net*을 입력합니다. **앱 ID URI**에 고유한 URI를 입력합니다. 그런 다음 **완료**를 선택합니다. ![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "두 번째 새 응용 프로그램")
5. AAD 응용 프로그램을 만들었으면 **구성** 탭으로 이동합니다. 이 탭에서 응용 프로그램에 대한 권한을 구성합니다.
6. **다른 응용 프로그램에 대한 권한**에서 **응용 프로그램 추가**를 선택하고 아래와 같이 다음 권한을 입력합니다. ![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "권한")
   
    응용 프로그램에 필요한 권한을 위임해야 합니다. ![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "권한 위임")
7. **키** 아래에서 기간을 선택합니다. 나중에 필요하므로 **키를 복사하여 안전한 곳에 저장**해 둡니다. __클라이언트 ID__도 적어 두어야 합니다. ![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "키 구성")
8. **single sign-on** 아래에서 __회신 URL__에 다음 URL을 입력합니다. https://msmanaged-na.consent.azure-apim.net/redirect: ![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "리디렉션 URL")
9. 변경 내용을 **저장**합니다. **키를 복사하여 안전한 곳에 저장**해 둡니다.

## PowerApps 또는 논리 흐름에서 연결 추가
AAD 응용 프로그램을 구성했으므로 이제 사용자 지정 API를 추가해 보겠습니다.

1. [PowerApps 웹 포털][1]을 열고 **연결** 탭으로 이동하여 오른쪽 위에서 __연결 추가__를 선택합니다. ![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "사용자 지정 API 만들기")
2. __사용자 지정 API 추가__를 선택합니다. ![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "사용자 지정 API 만들기")
3. [다운로드][8]할 수 있는 ARM Swagger 파일을 업로드합니다. ![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "사용자 지정 API 만들기")
4. 다음 화면에서는 AAD 인증을 사용하도록 Swagger 파일이 검색되었으므로 AAD 클라이언트 ID, 클라이언트 암호(안전한 곳에 저장된 **키**) 및 기타 설정을 입력해야 합니다. ![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "OAuth 설정")
5. 모든 항목이 올바르게 설정된 경우 연결을 만든 다음 PowerApp 또는 논리 흐름을 작성하는 동안 참조하여 ARM 사용자 지정 API를 사용할 수 있습니다. ![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "만들어진 사용자 지정 API")

이와 유사한 방식으로 RESTful API를 사용하여 노출되고 AAD OAuth2를 사용하여 인증되는 모든 데이터에 액세스할 수 있습니다.

PowerApps 및 논리 흐름을 만드는 환경에 대한 자세한 내용은 다음을 참조하세요.

* [Office 365, Twitter 및 Microsoft Translator에 연결][5]
* [Office 365의 데이터 표시 ][4]
* [데이터에서 앱 만들기][3]
* [논리 흐름 시작][2]

사용자 지정 API에 대한 질문이나 의견이 있는 경우 [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)으로 보내 주시기 바랍니다.

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0713_2016-->