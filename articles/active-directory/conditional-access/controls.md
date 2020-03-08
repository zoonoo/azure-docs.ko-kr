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
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671837"
---
# <a name="custom-controls-preview"></a>사용자 지정 컨트롤(미리 보기)

사용자 지정 컨트롤은 Azure Active Directory Premium P1 버전의 기능입니다. 사용자 지정 컨트롤을 사용하는 경우 Azure Active Directory 외부의 추가 요구 사항을 충족시키기 위해 사용자를 호환 가능한 서비스로 리디렉션합니다. 이 제어를 충족 하기 위해 사용자의 브라우저는 외부 서비스로 리디렉션되고 필요한 인증 또는 유효성 검사 작업을 수행한 다음 Azure Active Directory 다시 리디렉션됩니다. Azure Active Directory는 응답을 확인 하 고 사용자가 성공적으로 인증 되거나 유효성이 확인 되 면 사용자는 조건부 액세스 흐름을 계속 진행 합니다.

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

JSON 데이터를 복사한 다음 관련 텍스트 상자에 붙여넣습니다. 사용자가 변경한 내용을 명시적으로 이해 하지 않으면 JSON을 변경 하지 마세요. 변경하면 공급자와 Microsoft 간의 연결이 끊어져 잠재적으로 사용자가 사용자 계정 밖에서 잠길 수 있습니다.

사용자 지정 컨트롤을 만드는 옵션은 **조건부 액세스** 페이지의 **관리** 섹션에 있습니다.

![제어](./media/controls/82.png)

**새 사용자 지정 컨트롤**을 클릭하면 컨트롤의 JSON 데이터에 대한 텍스트 상자가 있는 블레이드가 열립니다.  

![제어](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>사용자 지정 컨트롤 삭제

사용자 지정 컨트롤을 삭제 하려면 먼저 조건부 액세스 정책에서 사용 되 고 있지 않은지 확인 해야 합니다. 작업이 완료되면 다음을 수행합니다.

1. 사용자 지정 컨트롤 목록으로 이동합니다.
1. …를 클릭합니다.  
1. **삭제**를 선택합니다.

## <a name="editing-custom-controls"></a>사용자 지정 컨트롤 편집

사용자 지정 컨트롤을 편집하려면 현재 컨트롤을 삭제하고 업데이트된 정보로 새 컨트롤을 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)

- [조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
