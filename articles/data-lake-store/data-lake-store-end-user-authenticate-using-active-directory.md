---
title: "Active Directory를 사용하여 Data Lake Store 인증 | Microsoft 문서"
description: "Active Directory를 사용하여 Data Lake Store로 인증하는 방법에 대해 알아봅니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7e7c9fd2fe7e6327cd39c4c26583c8fd556c9044


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Data Lake Store로 최종 사용자 인증
> [!div class="op_single_selector"]
> * [서비스 간 인증](data-lake-store-authenticate-using-active-directory.md)
> * [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store는 인증을 위해 Azure Active Directory를 사용합니다. Azure Data Lake Store 또는 Azure Data Lake Analytics와 함께 작동하는 응용 프로그램을 제작하기 전에 먼저 Azure Active Directory(Azure AD)를 사용하여 응용 프로그램을 인증하려는 방법을 결정해야 합니다. 사용할 수 있는 두 가지 주요 옵션은 다음과 같습니다.

* 최종 사용자 인증 및 
* 서비스 간 인증 

이 두 옵션 모두 Azure Data Lake Store 또는 Azure Data Lake Analytics에 대해 만들어진 각 요청에 연결하는 OAuth 2.0 토큰과 함께 제공되는 응용 프로그램에서 발생합니다.

이 문서는 최종 사용자 인증을 위한 Azure AD 웹 응용 프로그램을 만드는 방법에 대해 설명합니다. 서비스 간 인증을 위해 Azure AD 응용 프로그램 구성을 수행하는 방법은 [Azure Active Directory를 사용하여 Data Lake Store로 서비스 간 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 구독 ID. Azure Portal에서 검색할 수 있습니다. 예를 들어 Data Lake Store 계정 블레이드에서 사용할 수 있습니다.
  
    ![구독 ID 가져오기](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* Azure AD 도메인 이름. Azure Portal의 오른쪽 위 모서리에 마우스를 가져가서 검색할 수 있습니다. 아래 스크린샷에서 도메인 이름은 **contoso.microsoft.com**이며 괄호 안의 GUID는 테넌트 ID입니다. 
  
    ![AAD 도메인 가져오기](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>최종 사용자 인증
최종 사용자가 Azure AD를 통해 응용 프로그램에 로그인하기를 원하는 경우 권장되는 방법입니다. 응용 프로그램은 로그인한 최종 사용자와 동일한 수준의 액세스로 Azure 리소스에 액세스할 수 있게 됩니다. 최종 사용자는 응용 프로그램이 액세스를 유지할 수 있도록 주기적으로 자격 증명을 제공해야 합니다.

최종 사용자의 로그인으로 인해 응용 프로그램에 액세스 토큰 및 새로 고침 토큰이 제공됩니다. 액세스 토큰은 Data Lake Store 또는 Data Lake Analytics에 대해 만들어진 각 요청에 연결하며 기본적으로 1시간 동안 유효합니다. 새로 고침 토큰은 새 액세스 토큰을 가져오는 데 사용할 수 있고 정기적으로 사용되는 경우 기본적으로 최대 2주 동안 유효합니다. 최종 사용자 로그인에 두 가지 방법을 사용할 수 있습니다.

### <a name="using-the-oauth-20-pop-up"></a>OAuth 2.0 팝업 사용
응용 프로그램은 최종 사용자가 자격 증명을 입력할 수 있는 OAuth 2.0 권한 부여 팝업을 트리거할 수 있습니다. 이 팝업은 필요한 경우 Azure AD 2단계 인증(2FA) 프로세스와도 작동합니다. 

> [!NOTE]
> 이 방법은 Python 또는 Java용 Azure ADAL(AD 인증 라이브러리)에서 아직 지원되지 않습니다.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>사용자 자격 증명에서 직접 전달
응용 프로그램은 Azure AD에 사용자 자격 증명을 직접 제공할 수 있습니다. 이 방법은 조직 ID 사용자 계정에서만 작동됩니다. @outlook.com 또는 @live.com.로 끝나는 계정 등 개인 / "live ID" 사용자 계정과 호환되지 않습니다. 또한, 이 메서드는 Azure AD 2단계 인증(2FA)을 요구하는 사용자 계정과 호환되지 않습니다.

### <a name="what-do-i-need-to-use-this-approach"></a>이 방법을 사용하려면 무엇이 필요한가요?
* Azure AD 도메인 이름. 이 이름은 이 문서의 필수 구성 요소에 이미 나열되어 있습니다.
* Azure AD **웹 응용 프로그램**
* Azure AD 웹 응용 프로그램에 대한 클라이언트 ID
* Azure AD 웹 응용 프로그램의 회신 URI
* 위임된 권한 설정

Azure AD 웹 응용 프로그램을 만들고 아래 나열된 요구 사항을 위해 구성하는 방법은 아래의 [Active Directory 응용 프로그램 만들기](#create-an-active-directory-application) 섹션을 참조하세요. 

## <a name="create-an-active-directory-application"></a>Active Directory 응용 프로그램 만들기
이 섹션에서는 Azure Active Directory를 사용하여 Azure Data Lake Store로 최종 사용자 인증을 위한 Azure AD 웹 응용 프로그램을 만들고 구성하는 방법에 대해 알아봅니다.

### <a name="step-1-create-an-azure-active-directory-application"></a>1단계: Azure Active Directory 응용 프로그램을 만듭니다
> [!NOTE]
> 아래 단계에서는 Azure Portal을 사용합니다. 또한 [Azure PowerShell](../resource-group-authenticate-service-principal.md) 또는 [Azure CLI](../resource-group-authenticate-service-principal-cli.md)를 사용하여 Azure AD 응용 프로그램을 만들 수 있습니다.
> 
> 

1. [클래식 포털](https://manage.windowsazure.com/)을 통해 Azure 계정에 로그인합니다.
2. 왼쪽 창에서 **Active Directory** 를 선택합니다.
   
     ![Active Directory 선택](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. 새 응용 프로그램을 만드는 데 사용할 Active Directory를 선택합니다. Active Directory가 두 개 이상인 경우에는 일반적으로 구독이 상주하는 디렉터리에 응용 프로그램을 만듭니다. 구독과 동일한 디렉터리에 있는 응용 프로그램의 구독 리소스에만 액세스 권한을 부여할 수 있습니다.  
   
     ![디렉터리 선택](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. 디렉터리에서 응용 프로그램을 보려면 **응용 프로그램**을 클릭합니다.
   
     ![응용 프로그램 보기](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. 해당 디렉터리에서 응용 프로그램을 만든 적이 없는 경우 다음과 비슷한 이미지가 표시됩니다. **응용 프로그램 추가**를 클릭합니다.
   
     ![응용 프로그램 추가](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     또는 아래쪽 창에서 **추가** 를 클릭합니다.
   
     ![추가](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. 응용 프로그램의 이름을 입력하고 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 **웹 응용 프로그램 및/또는 웹 API** 를 만들기로 선택하고 다음 단추를 클릭합니다.
   
     ![응용 프로그램 이름 지정](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. 앱에 대한 속성을 입력합니다. **로그온 URL**의 경우 응용 프로그램을 설명하는 웹 사이트에 대한 URI를 제공합니다. 웹 사이트의 존재 여부는 확인되지 않습니다. 
   **앱 ID URI**의 경우 응용 프로그램을 식별하는 URI를 제공합니다.
   
     ![응용 프로그램 속성](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    확인 표시를 클릭해 마법사를 완료하고 응용 프로그램을 만듭니다.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>2단계: 클라이언트 ID, 회신 URI를 가져오고 위임된 권한을 설정합니다
1. **구성** 탭을 클릭하여 응용 프로그램의 암호를 구성합니다.
   
     ![응용 프로그램 구성](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. **클라이언트 ID**를 복사합니다.
   
     ![클라이언트 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. **Single sign-on** 섹션에서 **회신 URI**를 복사합니다.
   
    ![클라이언트 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. **다른 응용 프로그램에 대한 권한**에서 **응용 프로그램 추가**를 클릭합니다.
   
    ![클라이언트 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. **다른 응용 프로그램에 대한 권한** 마법사에서 **Azure Data Lake** 및 **Windows** **Azure Service Management API**를 선택한 다음 확인 표시를 클릭합니다.
6. 기본적으로 새로 추가된 서비스의 **위임된 권한**은 0으로 설정됩니다. Azure Data Lake 및 Windows Azure Management Service의 **위임된 권한** 드롭다운을 클릭하고 사용 가능한 확인란을 선택해 값을 1로 설정합니다. 결과는 다음과 같습니다.
   
     ![클라이언트 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. **Save**를 클릭합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure AD 웹 응용 프로그램을 만들고 .NET SDK, Java SDK 등을 사용하여 만든 클라이언트 응용 프로그램에 필요한 정보를 수집했습니다. 이제 다음 문서를 읽고 Azure AD 웹 응용 프로그램을 사용하여 Data Lake Store로 인증한 다음 저장소에서 다른 작업을 수행하는 방법에 대해 알아볼 수 있습니다.

* [.NET SDK를 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-net-sdk.md)
* [Java SDK를 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO3-->


