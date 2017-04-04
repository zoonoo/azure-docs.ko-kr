---
title: "인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기 | Microsoft Docs"
description: "인증서 자격 증명과 함께 Azure AD Reporting API를 사용하여 사용자 개입 없이 디렉터리에서 데이터를 가져오는 방법에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/24/2017
ms.author: ramical
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: c1345dcda6e52267a8037ffd7207e6bc3b0d3b31
ms.lasthandoff: 03/28/2017


---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기
이 문서에서는 인증서 자격 증명과 함께 Azure AD Reporting API를 사용하여 사용자 개입 없이 디렉터리에서 데이터를 가져오는 방법에 대해 설명합니다. 

## <a name="use-the-azure-ad-reporting-api"></a>Azure AD Reporting API 사용 
Azure AD Reporting API은 다음 단계를 완료해야 합니다.
 *    필수 구성 요소 설치
 *    앱에서 인증서 설정
 *    액세스 토큰 가져오기
 *    액세스 토큰을 사용하여 Graph API 호출

원본 코드에 대한 정보는 [보고서 API 모듈 활용](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils)을 참조하세요. 

### <a name="install-prerequisites"></a>필수 구성 요소 설치
Azure AD PowerShell V2 및 AzureADUtils 모듈을 설치해야 합니다.

1. [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md)의 지침에 따라 Azure AD Powershell V2를 다운로드하고 설치합니다.
2. [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1)에서 Azure AD 유틸리티 모듈을 다운로드합니다. 
  이 모듈에서는 다음을 비롯한 몇 가지 유틸리티 cmdlet을 제공합니다.
   * NuGet을 사용하는 최신 버전의 ADAL
   * ADAL을 사용하는 사용자, 응용 프로그램 키 및 인증서의 액세스 토큰
   * Graph API를 처리하는 페이지 단위의 결과

**Azure AD 유틸리티 모듈을 설치하려면:**

1. 디렉터리를 만들어서 유틸리티 모듈(예: c:\azureAD)을 저장하고 GitHub에서 모듈을 다운로드합니다.
2. PowerShell 세션을 열고 방금 만든 디렉터리로 이동합니다. 
3. 모듈을 가져오고 Install-AzureADUtilsModule cmdlet을 사용하여 PowerShell 모듈 경로에 설치합니다. 

세션은 이 화면과 유사하게 표시됩니다.

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>앱에서 인증서 설정
1. 앱이 이미 있는 경우 Azure Portal에서 개체 ID를 가져옵니다. 

  ![Azure 포털](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. PowerShell 세션을 열고 Connect-AzureAD cmdlet을 사용하여 Azure AD에 연결합니다.

  ![Azure 포털](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. AzureADUtils에서 New-AzureADApplicationCertificateCredential cmdlet을 사용하여 여기에 인증서 자격 증명을 추가합니다. 

>[!Note]
>인증서 개체뿐만 아니라 이전에 캡처한 응용 프로그램 개체 ID를 제공해야 합니다(Cert: 드라이브를 사용하여 가져옴).
>


  ![Azure 포털](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>액세스 토큰 가져오기

액세스 토큰을 가져오려면 AzureADUtils에서 Get-AzureADGraphAPIAccessTokenFromCert cmdlet을 사용합니다. 

>[!NOTE]
>마지막 섹션에서 사용한 개체 ID가 아닌 응용 프로그램 ID를 사용해야 합니다.
>

 ![Azure 포털](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>액세스 토큰을 사용하여 Graph API 호출

이제 스크립트를 만들 수 있습니다. 다음은 AzureADUtils에서 Invoke-AzureADGraphAPIQuery cmdlet을 사용하는 예제입니다. 이 cmdlet은 여러 페이지 단위의 결과를 처리한 다음 PowerShell 파이프라인에 해당 결과를 보냅니다. 

 ![Azure 포털](./media/active-directory-report-api-with-certificates/script-completed.png)

이제 CSV로 내보내고 SIEM 시스템에 저장할 준비가 되었습니다. 예약된 태스크에서 스크립트를 래핑하여 원본 코드에서 응용 프로그램 키를 저장하지 않고 주기적으로 테넌트에서 Azure AD 데이터를 가져올 수도 있습니다. 

## <a name="next-steps"></a>다음 단계
[Azure ID 관리의 기본 항목](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>




