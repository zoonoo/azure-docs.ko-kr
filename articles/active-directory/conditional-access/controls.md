---
title: Azure AD 조건부 액세스의 사용자 지정 컨트롤
description: Azure Active Directory 조건부 액세스의 사용자 지정 컨트롤의 작동 방식에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1df037b66c72177a96f77231cee70782d04992
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620703"
---
# <a name="custom-controls-preview"></a>사용자 지정 컨트롤(미리 보기)

사용자 지정 컨트롤은 Azure Active Directory Premium P1 버전의 기능입니다. 사용자 지정 컨트롤을 사용하는 경우 Azure Active Directory 외부의 추가 요구 사항을 충족시키기 위해 사용자를 호환 가능한 서비스로 리디렉션합니다. 이 제어를 충족시키기 위해 사용자의 브라우저는 외부 서비스로 리디렉션되고 필요한 인증 또는 유효성 검사 활동을 수행한 다음 Azure Active Directory로 다시 리디렉션됩니다. Azure Active Directory는 응답을 확인 하 고 사용자가 성공적으로 인증 되거나 유효성이 확인 되 면 사용자는 조건부 액세스 흐름을 계속 진행 합니다.

이러한 컨트롤을 사용 하면 조건부 액세스 제어로 특정 외부 또는 사용자 지정 서비스를 사용할 수 있으며, 일반적으로 조건부 액세스의 기능을 확장할 수 있습니다.

현재 호환 서비스를 제공하는 공급자는 다음과 같습니다.

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

이러한 서비스에 대한 자세한 내용은 공급자에게 직접 문의하세요.

## <a name="creating-custom-controls"></a>사용자 지정 컨트롤 만들기

사용자 지정 컨트롤을 만들려면 먼저 사용할 공급자에게 문의해야 합니다. 각 비 Microsoft 공급자에는 등록, 구독 또는 서비스의 일부가 될 수 있는 고유한 프로세스 및 요구 사항이 있으며 조건부 액세스와 통합 하려는 경우를 표시 합니다. 이 시점에서 공급자는 JSON 형식의 데이터 블록을 제공합니다. 이 데이터를 사용 하면 공급자 및 조건부 액세스가 테 넌 트에 대해 함께 작동 하 고, 새 컨트롤이 만들어지고, 사용자가 공급자를 사용 하 여 확인을 성공적으로 수행 했는지 여부를 조건부 액세스에서 확인할 수 있는 방법을 정의 합니다.

사용자 지정 컨트롤은 multi-factor authentication을 요구 하는 Id 보호의 자동화와 함께 사용 하거나 PIM (권한 있는 Id 관리자)에서 역할을 승격 시킬 수 없습니다.

JSON 데이터를 복사한 다음 관련 텍스트 상자에 붙여넣습니다. 변경 사항을 명시적으로 이해하지 못한 경우 JSON을 변경하지 마세요. 변경하면 공급자와 Microsoft 간의 연결이 끊어져 잠재적으로 사용자가 사용자 계정 밖에서 잠길 수 있습니다.

사용자 지정 컨트롤을 만드는 옵션은 **조건부 액세스** 페이지의 **관리** 섹션에 있습니다.

![Control](./media/controls/82.png)

**새 사용자 지정 컨트롤**을 클릭하면 컨트롤의 JSON 데이터에 대한 텍스트 상자가 있는 블레이드가 열립니다.  

![Control](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>사용자 지정 컨트롤 삭제

사용자 지정 컨트롤을 삭제 하려면 먼저 조건부 액세스 정책에서 사용 되 고 있지 않은지 확인 해야 합니다. 작업이 완료되면 다음을 수행합니다.

1. 사용자 지정 컨트롤 목록으로 이동합니다.
1. …를 클릭합니다.  
1. **삭제**를 선택합니다.

## <a name="editing-custom-controls"></a>사용자 지정 컨트롤 편집

사용자 지정 컨트롤을 편집하려면 현재 컨트롤을 삭제하고 업데이트된 정보로 새 컨트롤을 만들어야 합니다.

## <a name="session-controls"></a>세션 컨트롤

세션 컨트롤은 클라우드 앱 내에서 제한된 환경을 활성화합니다. 세션 컨트롤은 클라우드 앱에서 적용되고 Azure AD가 앱에 제공한 세션에 대한 추가 정보에 의존합니다.

![Control](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>앱에서 적용된 제한 사항 사용

이 컨트롤을 사용하여 Azure AD가 디바이스 정보를 선택한 클라우드 앱에 전달하도록 할 수 있습니다. 디바이스 정보를 사용하면 클라우드 앱이 규정 준수 또는 도메인 조인 디바이스에서 연결이 초기화되는지 여부를 알 수 있습니다. 이 컨트롤은 선택한 클라우드 앱으로 SharePoint Online 및 Exchange Online만 지원합니다. 선택하면 클라우드 앱에서는 디바이스 정보를 사용하여 사용자에게 디바이스 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.

자세히 알아보려면 다음을 참조하십시오.

- [SharePoint Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/spolimitedaccessdocs)
- [Exchange Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.
- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 Azure Active Directory의 [조건부 액세스 모범 사례](best-practices.md)를 참조하세요.
