---
title: 인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기 | Microsoft Docs
description: 인증서 자격 증명과 함께 Azure AD Reporting API를 사용하여 사용자 개입 없이 디렉터리에서 데이터를 가져오는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c1f5b88dc8cd6d7f8ff1cd1269d4791d5654e133
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145231"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>인증서와 함께 Azure Active Directory Reporting API를 사용하여 데이터 가져오기

[Azure AD(Azure Active Directory) Reporting API](concept-reporting-api.md)는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다. 사용자 작업 없이 Azure AD Reporting API에 액세스하려면 인증서를 사용하도록 액세스를 구성하면 됩니다.

다음 단계를 포함합니다.

1. [필수 구성 요소 설치](#install-prerequisites)
2. [앱에 인증서 등록](#register-the-certificate-in-your-app)
3. [MS Graph API에 대한 액세스 토큰 가져오기](#get-an-access-token-for-ms-graph-api)
4. [MS Graph API 엔드포인트 쿼리](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>필수 구성 요소 설치

1. 먼저 [Azure Active Directory 보고 API에 액세스하기 위한 필수 조건](howto-configure-prerequisites-for-reporting-api.md)을 완료했는지 확인합니다. 

2. [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md)의 지침에 따라 Azure AD Powershell V2를 다운로드하고 설치합니다.

3. [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/)에서 MSCloudIDUtils를 설치합니다. 이 모듈에서는 다음을 비롯한 몇 가지 유틸리티 cmdlet을 제공합니다.
    - 인증에 필요한 ADAL 라이브러리
    - ADAL을 사용하는 사용자, 응용 프로그램 키 및 인증서의 액세스 토큰
    - Graph API를 처리하는 페이지 단위의 결과

4. 모듈을 처음 사용하는 경우, **Install-MSCloudIdUtilsModule**을 실행하여 설치를 완료하고, 처음 사용하는 경우가 아니면 **Import-Module** Powershell 명령을 사용하여 간단히 가져올 수 있습니다.

세션은 다음 화면과 유사하게 표시됩니다.

  ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>앱에 인증서 등록

1. 먼저 응용 프로그램 등록 페이지로 이동합니다. [Azure Portal](https://portal.azure.com)로 이동하고 **Azure Active Directory**를 클릭한 다음, **앱 등록**을 클릭하고 목록에서 해당 응용 프로그램을 선택하면 이 작업을 수행할 수 있습니다. 

2. 그런 다음, 단계에 따라 응용 프로그램에 대한 [인증서를 Azure AD에 등록](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad)합니다. 

3. 방금 응용 프로그램에 등록한 인증서의 지문 및 응용 프로그램 ID를 적어 둡니다. 지문을 찾으려면 포털의 응용 프로그램 페이지에서 **설정**으로 이동하고 **키**를 클릭합니다. **공개 키** 목록 아래에 지문이 표시됩니다.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>MS Graph API에 대한 액세스 토큰 가져오기

MS Graph API에 대한 액세스 토큰을 가져오려면 MSCloudIdUtils PowerShell 모듈의 **Get-MSCloudIdMSGraphAccessTokenFromCert** cmdlet을 사용합니다. 

>[!NOTE]
>응용 프로그램 ID(ClientID라고도 함) 및 개인 키가 컴퓨터의 인증서 저장소(CurrentUser 또는 LocalMachine 인증서 저장소)에 설치된 인증서의 인증서 지문을 사용해야 합니다.
>

 ![Azure portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>액세스 토큰을 사용하여 Graph API 호출

이제, Powershell 스크립트에서 액세스 토큰을 사용하여 Graph API를 쿼리할 수 있습니다. 다음은 MSCloudIDUtils의 **Invoke-MSCloudIdMSGraphQuery** cmdlet을 사용하여 signins 또는 diectoryAudits 엔드포인트를 열거하는 예제입니다. 이 cmdlet은 여러 페이지 단위의 결과를 처리한 다음 PowerShell 파이프라인에 해당 결과를 보냅니다.

### <a name="query-the-directoryaudits-endpoint"></a>DirectoryAudits 엔드포인트 쿼리
 ![Azure portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>SignIns 엔드포인트 쿼리
 ![Azure portal](./media/tutorial-access-api-with-certificates/query-signins.png)

이제 이 데이터를 CSV로 내보내고 SIEM 시스템에 저장하도록 선택할 수 있습니다. 예약된 태스크에서 스크립트를 래핑하여 원본 코드에서 응용 프로그램 키를 저장하지 않고 주기적으로 테넌트에서 Azure AD 데이터를 가져올 수도 있습니다. 


## <a name="next-steps"></a>다음 단계

* [reporting API의 첫 인상 살펴보기](concept-reporting-api.md)
* [감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [로그인 활동 보고서 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



