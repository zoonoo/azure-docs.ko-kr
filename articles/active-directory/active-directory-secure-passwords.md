---
title: "Azure AD에서 암호 보안 및 스마트 암호 잠금에 의해 차단된 암호 다시 설정 | Microsoft Docs"
description: "Azure AD 테넌트의 정의 및 Azure Active Directory를 통해 Azure를 관리하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Azure AD에서 암호 보안 및 스마트 암호 잠금에 의해 차단된 암호 다시 설정
이 문서에서는 Azure AD(Azure Active Directory) 및 Microsoft 계정 서비스 계정을 보호하기 위해 사용자 또는 관리자로서 수행할 수 있는 모범 사례를 설명합니다. 

 >[!NOTE]
 >Azure AD 관리자는 디렉터리 이름을 클릭하여 사용자 암호 다시 설정할 수 있습니다. [Azure 관리 포털](https://manage.windowsazure.com)에서 [사용자] 페이지를 선택하고 사용자의 이름 및 암호 다시 설정을 클릭합니다. 
 >

Azure AD는 암호를 보호하기 위해 다음과 같은 일반적인 접근 방식을 포함합니다.
 *    암호 길이 요구 사항
 *    암호 “복잡성” 요구 사항
 *    일반 및 정기적인 암호 만료 

암호 관리 기능에 대한 정보는 [Azure Active Directory에서 암호 관리](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords)를 참조하세요. 

## <a name="azure-ad-password-protection"></a>Azure AD 암호 보호
Azure AD 및 Microsoft 계정 시스템은 사용자 및 관리자 암호를 안전하게 보호하기 위해 업계에서 증명된 접근 방법을 사용합니다. 

이 섹션에서는 다음 메서드를 사용하여 Azure AD에서 암호를 보호하는 방법을 설명합니다.
 *    동적으로 금지된 암호
 *    스마트 암호 잠금

현재 연구 결과에 따른 암호 관리에 대한 내용은 [암호 지침](http://aka.ms/passwordguidance) 백서를 참조하세요. 

### <a name="dynamically-banned-passwords"></a>동적으로 금지된 암호
Azure AD 및 Microsoft 계정 시스템은 일반적으로 사용되는 모든 암호를 동적으로 금지함으로써 암호 보호를 보호합니다. Azure AD ID 보호 팀은 금지된 암호 목록을 정기적으로 분석하여 사용자가 자주 사용되는 암호를 선택하지 않도록 방지합니다. Azure AD 및 Microsoft 계정 서비스 고객은 이 서비스를 사용할 수 있습니다. 

암호를 만들 때 관리자는 사용자가 고유한 문자, 숫자 및 문자 조합을 포함하는 특별한 암호 구문을 선택했는지 확인해야 합니다. 이를 통해 손상될 가능성이 거의 없는 사용자 암호를 만들 수 있습니다. 

**위반 목록**

Azure AD에서는 항상 한 발 앞서 사이버 범죄에 대처하려고 합니다. 이를 위한 한 가지 방법은 사용자가 현재 공격 목록에 있는 암호를 만들지 않도록 방지하는 것입니다.

Azure AD ID 보호 팀은 일반적으로 사용되는 암호를 지속적으로 분석합니다. 사이버 범죄자들도 암호 해시를 공격하는 [레인보우 테이블](https://en.wikipedia.org/wiki/Rainbow_table)을 빌드하는 등 공격을 알리는 데 유사한 전략을 사용합니다. 

Microsoft는 지속적으로 [데이터 위반](https://www.privacyrights.org/data-breaches)을 분석하여 동적으로 업데이트된 차단 암호 목록을 관리합니다. 그러면 취약한 암호가 Azure AD 고객에게 실제 위협이 되기 전에 차단할 수 있습니다. 현재 보안 노력에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/sir/default.aspx)를 참조하세요. 

### <a name="smart-password-lockout"></a>스마트 암호 잠금

Azure AD에서 사용자 암호를 해킹하려는 잠재적인 사이버 범죄를 감지하면 스마트 암호 잠금을 사용하여 사용자 계정을 잠그게 됩니다. Azure AD는 특정 로그인 세션과 연결된 위험성을 확인하도록 설계되었습니다. 

최신 보안 데이터를 사용하여 사이버 위협에 잠금 의미 체계를 적용합니다. 사이버 범죄가 네트워크의 사용자 암호를 해킹하는 경우에도 이러한 방법으로 사용자가 차단되지 않습니다.

사용자가 Azure AD에서 차단된 경우 해당 화면은 아래와 비슷하게 표시됩니다.

  ![Azure AD에서 차단](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
그리고 다른 Microsoft 계정의 경우 해당 화면은 아래와 비슷하게 표시됩니다.

  ![Microsoft 계정에서 차단](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Azure Active Directory에서 암호 관리에 대한 정보는 [암호 관리 작동 방법](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works)을 참조하세요.

  >![참고] Azure AD 관리자인 경우 [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello)를 사용하여 사용자가 기존 암호를 모두 만들지 않도록 할 수 있습니다.
  >

## <a name="next-steps"></a>다음 단계
[고유한 암호를 업데이트하는 방법](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Azure ID 관리의 기본 항목](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[암호 관리 보고서를 사용하여 Operational Insights를 얻는 방법](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



