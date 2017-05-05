---
title: "서비스 간 인증: Azure Active Directory를 사용하여 Data Lake Store로 | Microsoft Docs"
description: "Azure Active Directory를 사용하여 Data Lake Store로 서비스 간 인증을 수행하는 방법을 알아봅니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 1d712ef6987a4af2014bedb54378f288bcf535a8
ms.lasthandoff: 04/22/2017


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Data Lake Store로 서비스 간 인증
> [!div class="op_single_selector"]
> * [서비스 간 인증](data-lake-store-authenticate-using-active-directory.md)
> * [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store는 인증을 위해 Azure Active Directory를 사용합니다. Azure Data Lake Store 또는 Azure Data Lake Analytics와 함께 작동하는 응용 프로그램을 제작하기 전에 먼저 Azure Active Directory(Azure AD)를 사용하여 응용 프로그램을 인증하려는 방법을 결정해야 합니다. 사용할 수 있는 두 가지 주요 옵션은 다음과 같습니다.

* 최종 사용자 인증 
* 서비스 간 인증(이 문서) 

이 두 옵션 모두 Azure Data Lake Store 또는 Azure Data Lake Analytics에 대해 만들어진 각 요청에 연결하는 OAuth 2.0 토큰과 함께 제공되는 응용 프로그램에서 발생합니다.

이 문서는 **서비스 간 인증을 위한 Azure AD 웹 응용 프로그램을 만드는** 방법에 대해 설명합니다. 최종 사용자 인증을 위해 Azure AD 응용 프로그램 구성을 수행하는 방법은 [Azure Active Directory를 사용하여 Data Lake Store로 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="step-1-create-an-active-directory-web-application"></a>1단계: Active Directory 웹 응용 프로그램 만들기

Azure Active Directory를 사용하여 Azure Data Lake Store로 서비스 간 인증을 위한 Azure AD 웹 응용 프로그램을 만들고 구성합니다. 지침에 대해서는 [Azure AD 응용 프로그램 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)를 참조하세요.

위의 링크에 있는 지침을 수행하는 동안 아래 스크린샷과 같이 응용 프로그램 유형으로 **웹앱/API**를 선택해야 합니다.

![웹앱 만들기](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "웹앱 만들기")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>2단계: 응용 프로그램 ID, 인증 키 및 테넌트 ID 가져오기
프로그래밍 방식으로 로그인하는 경우 응용 프로그램에 대한 ID가 필요합니다. 응용 프로그램이 자체 자격 증명에서 실행되는 경우 인증 키도 필요합니다.

* 응용 프로그램에 대한 응용 프로그램 ID 및 인증 키(클라이언트 비밀이라고도 함)를 검색하는 방법에 대한 지침은 [응용 프로그램 ID 및 인증 키 가져오기](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)를 참조하세요.

* 테넌트 ID를 검색하는 방법에 대한 지침은 [테넌트 ID 가져오기](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)를 참조하세요.

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>3단계: Azure Data Lake Store 계정 파일 또는 폴더에 Azure AD 응용 프로그램 할당(서비스 간 인증에만 해당)
1. 새 [Azure Portal](https://portal.azure.com)에 로그온하여 이전에 만든 Azure Active Directory 응용 프로그램에 연결할 Azure Data Lake Store 계정을 엽니다.
2. 데이터 레이크 저장소 계정 블레이드에서 **데이터 탐색기**를 클릭합니다.
   
    ![Data Lake Store 계정에 디렉터리 만들기](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Data Lake Store 계정에 디렉터리 만들기")
3. **데이터 탐색기** 블레이드에서 Azure AD 응용 프로그램에 대한 액세스를 제공할 파일 또는 폴더를 클릭하고 **액세스**를 클릭합니다. 파일에 대한 액세스를 구성하려면 **파일 미리 보기** 블레이드에서 **액세스**를 클릭해야 합니다.
   
    ![Data Lake 파일 시스템에 ACL 설정](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Data Lake 파일 시스템에 ACL 설정")
4. **액세스** 블레이드는 루트에 이미 할당된 표준 액세스 및 사용자 지정 액세스를 나열합니다. **추가** 아이콘을 클릭하여 사용자 지정 수준 ACL을 추가합니다.
   
    ![표준 및 사용자 지정 액세스 나열](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "표준 및 사용자 지정 액세스 나열")
5. **추가** 아이콘을 클릭하여 **사용자 지정 액세스 추가** 블레이드를 엽니다. 이 블레이드에서 **사용자 또는 그룹 선택**을 클릭한 다음 **사용자 또는 그룹 선택** 블레이드에서 이전에 만든 Azure Active Directory 응용 프로그램을 찾습니다. 검색할 그룹이 많을 경우 위쪽의 텍스트 상자를 사용하여 그룹 이름을 필터링합니다. 추가하려는 그룹을 클릭한 다음 **선택**을 클릭합니다.
   
    ![그룹 추가](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "그룹 추가")
6. **사용 권한 선택**을 클릭하고 사용 권한 및 이러한 권한을 기본 ACL로 할당할지, 액세스 ALC로 할당할지 또는 둘 다로 할당할지 선택합니다. **확인**을 클릭합니다.
   
    ![그룹에 권한 할당](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "그룹에 권한 할당")
   
    Data Lake Store의 사용 권한 및 기본/액세스 ACL에 대한 자세한 내용은 [Data Lake Store에서 액세스 제어](data-lake-store-access-control.md)를 참조하세요.
7. **사용자 지정 액세스 추가** 블레이드에서 **확인**을 클릭합니다. 이제 연결된 권한으로 새롭게 추가된 그룹이 **액세스** 블레이드에 나열됩니다.
   
    ![그룹에 권한 할당](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "그룹에 권한 할당")

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>4단계: OAuth 2.0 토큰 끝점 가져오기(Java 기반 응용 프로그램만 해당)

1. 새 [Azure Portal](https://portal.azure.com)에 로그온하고 왼쪽 창에서 Active Directory를 클릭합니다.

2. 왼쪽 창에서 **앱 등록**을 클릭합니다.

3. 앱 등록 블레이드 맨 위에서 **끝점**을 클릭합니다.

    ![OAuth 토큰 끝점](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth 토큰 끝점")

4. 끝점 목록에서 OAuth 2.0 토큰 끝점을 복사합니다.

    ![OAuth 토큰 끝점](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth 토큰 끝점")   

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure AD 웹 응용 프로그램을 만들고 .NET SDK, Java SDK 등을 사용하여 만든 클라이언트 응용 프로그램에 필요한 정보를 수집했습니다. 이제 다음 문서를 읽고 Azure AD 웹 응용 프로그램을 사용하여 Data Lake Store로 인증한 다음 저장소에서 다른 작업을 수행하는 방법에 대해 알아볼 수 있습니다.

* [.NET SDK를 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-net-sdk.md)
* [Java SDK를 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-java-sdk.md)

이 문서에서는 응용 프로그램에 대해 사용자 주체를 작동하고 실행하는 데 필요한 기본 단계를 안내했습니다. 다음 문서에서 추가 정보를 찾을 수 있습니다.
* [PowerShell을 사용하여 서비스 주체 만들기](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [서비스 주체 인증에 인증서 인증 사용](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Azure AD에 인증하는 다른 방법](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)



