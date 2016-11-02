<properties
   pageTitle="Active Directory를 사용하여 Data Lake Store로 인증 | Microsoft Azure"
   description="Active Directory를 사용하여 Data Lake Store로 인증하는 방법에 대해 알아봅니다."
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>


# <a name="authenticate-with-data-lake-store-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Data Lake Store로 인증

Azure Data Lake Store는 인증을 위해 Azure Active Directory를 사용합니다. Azure Data Lake Store 또는 Azure Data Lake Analytics와 함께 작동하는 응용 프로그램을 제작하기 전에 먼저 Azure Active Directory(Azure AD)를 사용하여 응용 프로그램을 인증하려는 방법을 결정해야 합니다. 사용할 수 있는 두 가지 주요 옵션은 다음과 같습니다.

* 최종 사용자 인증 및 
* 서비스 간 인증 

이 두 옵션 모두 Azure Data Lake Store 또는 Azure Data Lake Analytics에 대해 만들어진 각 요청에 연결하는 OAuth 2.0 토큰과 함께 제공되는 응용 프로그램에서 발생합니다.


## <a name="prerequisites"></a>필수 조건

* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 구독 ID. Azure Portal에서 검색할 수 있습니다. 예를 들어 Data Lake Store 계정 블레이드에서 사용할 수 있습니다.

    ![구독 ID 가져오기](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD 도메인 이름. Azure Portal의 오른쪽 위 모서리에 마우스를 가져가서 검색할 수 있습니다. 

    ![AAD 도메인 가져오기](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>최종 사용자 인증

최종 사용자가 Azure AD를 통해 응용 프로그램에 로그인하기를 원하는 경우 권장되는 방법입니다. 응용 프로그램은 로그인한 최종 사용자와 동일한 수준의 액세스로 Azure 리소스에 액세스할 수 있게 됩니다. 최종 사용자는 응용 프로그램이 액세스를 유지할 수 있도록 주기적으로 자격 증명을 제공해야 합니다.

최종 사용자의 로그인으로 인해 응용 프로그램에 액세스 토큰 및 새로 고침 토큰이 제공됩니다. 액세스 토큰은 Data Lake Store 또는 Data Lake Analytics에 대해 만들어진 각 요청에 연결하며 기본적으로 1시간 동안 유효합니다. 새로 고침 토큰은 새 액세스 토큰을 가져오는 데 사용할 수 있고 정기적으로 사용되는 경우 기본적으로 최대 2주 동안 유효합니다. 최종 사용자 로그인에 두 가지 방법을 사용할 수 있습니다.

### <a name="using-the-oauth-2.0-pop-up"></a>OAuth 2.0 팝업 사용

응용 프로그램은 최종 사용자가 자격 증명을 입력할 수 있는 OAuth 2.0 권한 부여 팝업을 트리거할 수 있습니다. 이 팝업은 필요한 경우 Azure AD 2단계 인증(2FA) 프로세스와도 작동합니다. 

>[AZURE.NOTE] 이 방법은 Python 또는 Java용 Azure ADAL(AD 인증 라이브러리)에서 아직 지원되지 않습니다.

### <a name="directly-passing-in-user-credentials"></a>사용자 자격 증명에서 직접 전달

응용 프로그램은 Azure AD에 사용자 자격 증명을 직접 제공할 수 있습니다. 이 방법은 조직 ID 사용자 계정에서만 작동됩니다. @outlook.com 또는 @live.com.로 끝나는 계정 등 개인 / "live ID" 사용자 계정과 호환되지 않습니다. 또한, 이 메서드는 Azure AD 2단계 인증(2FA)을 요구하는 사용자 계정과 호환되지 않습니다.

### <a name="what-do-i-need-to-use-this-approach?"></a>이 방법을 사용하려면 무엇이 필요한가요?

* Azure AD 도메인 이름(이 문서의 필수 구성 요소에 이미 나열되어 있음).

* Azure AD **네이티브 클라이언트 응용 프로그램**. 

* Azure AD 네이티브 클라이언트 응용 프로그램에 대한 클라이언트 ID.

* Azure AD 네이티브 클라이언트 응용 프로그램에 대한 리디렉션 URI입니다. 

Azure AD 응용 프로그램을 만들고 클라이언트 ID를 검색하는 방법에 대한 자세한 내용은 [Active Directory 응용 프로그램 만들기](../resource-group-create-service-principal-portal.md#create-an-active-directory-application)를 참조하세요. 

>[AZURE.NOTE] 위의 링크에서 지침은 Azure AD 웹 응용 프로그램용입니다. 그러나 대신 네이티브 클라이언트 응용 프로그램을 만들더라도 단계는 정확하게 동일합니다.

## <a name="service-to-service-authentication"></a>서비스 간 인증

최종 사용자가 자격 증명을 제공할 필요 없이 응용 프로그램이 자동으로 Azure AD로 인증하도록 하려는 경우 권장되는 방법입니다. 해당 자격 증명이 유효하기만 하면 응용 프로그램은 자체적으로 인증할 수 있습니다. 이는 연도의 순서로 사용자 지정될 수 있습니다.

### <a name="what-do-i-need-to-use-this-approach?"></a>이 방법을 사용하려면 무엇이 필요한가요?

* Azure AD 도메인 이름(이 문서의 필수 구성 요소에 이미 나열되어 있음).

* Azure AD **웹 응용 프로그램**.

* Azure AD 웹 응용 프로그램에 대한 클라이언트 ID.

    >[AZURE.NOTE] Azure AD 응용 프로그램을 만들고 클라이언트 ID를 검색하는 방법에 대한 자세한 내용은 [Active Directory 응용 프로그램 만들기](../resource-group-create-service-principal-portal.md#create-an-active-directory-application)를 참조하세요.
    
* 클라이언트 암호 또는 인증서를 사용하도록 Azure AD 웹 응용 프로그램을 구성합니다. 인증서를 사용하여 웹 응용 프로그램을 만들려면 [인증서를 사용하여 서비스 사용자 만들기](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)를 참조하세요.

* 함께 작동하려는 Data Lake Store 파일/폴더 또는 Data Lake Analytics 계정에서 Azure AD 웹 응용 프로그램에 대한 액세스를 사용하도록 설정합니다. Data Lake Store 파일/폴더에 Azure AD 응용 프로그램에 대한 액세스를 제공하는 방법에 대한 지침은 [Azure Data Lake Store 파일 시스템에 ACL로 사용자 또는 보안 그룹 할당](data-lake-store-secure-data.md#filepermissions)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [.NET SDK를 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-net-sdk.md)
- [Java SDK를 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-java-sdk.md)



<!--HONumber=Oct16_HO2-->


