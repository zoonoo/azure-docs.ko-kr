---
title: Azure Active Directory 디바이스 관리 FAQ | Microsoft Docs
description: Azure Active Directory 디바이스 관리 FAQ
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 8d1e44eae7e87a450ac5f36e621d559fca92ca74
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016157"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 디바이스 관리 FAQ

**Q: 최근에 디바이스를 등록했습니다. Azure Portal에서 내 사용자 정보에 디바이스가 표시되지 않는 이유는 무엇인가요? 또는 디바이스 소유자가 하이브리드 Azure AD 조인 디바이스에 대해 N/A로 표시되는 이유는 무엇인가요?**
**A:** 하이브리드 Azure AD 조인 Windows 10 디바이스는 사용자 디바이스 아래에 표시되지 않습니다.
Azure Portal에서 모든 디바이스 보기를 사용해야 합니다. PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet을 사용할 수도 있습니다.

다음 디바이스만 사용자 디바이스 아래에 나열됩니다.

- 하이브리드 Azure AD에 가입되지 않은 모든 개인 디바이스 
- 모든 비 Windows 10/Windows Server 2016 디바이스
- 모든 비 Windows 디바이스 

--- 

**Q: 클라이언트의 디바이스 등록 상태를 어떻게 알 수 있나요?**

**A:** Azure Portal에서 모든 디바이스로 이동한 다음, 디바이스 ID를 사용하여 디바이스를 검색할 수 있습니다. 조인 유형 열 아래의 값을 확인합니다. 경우에 따라 디바이스를 다시 설정하거나 이미지로 다시 설치할 수 있습니다. 따라서 디바이스에서도 디바이스 등록 상태를 반드시 확인해야 합니다.

- Windows 10 및 Windows Server 2016 이상 디바이스의 경우, dsregcmd.exe /status를 실행합니다.
- 하위 수준 OS 버전의 경우 "%programFiles%\Microsoft Workplace Join\autoworkplace.exe"를 실행합니다.

---

**Q: Azure Portal에서 사용자 정보에 디바이스 레코드가 표시되고 디바이스에 등록된 상태로 표시됩니다. 조건부 액세스를 사용할 수 있게 제대로 설정되어 있는 것인가요?**

**A:** deviceID로 리플렉션된 디바이스 가입 상태는 Azure AD의 가입 상태와 일치해야 하며 조건부 액세스에 대한 평가 조건을 만족해야 합니다. 자세한 내용은 [조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 관리 디바이스 필요](../conditional-access/require-managed-devices.md)를 참조하세요.

---

**Q: Azure Portal에서 또는 Windows PowerShell을 사용하여 삭제했는데 디바이스의 로컬 상태가 여전히 등록되어 있다고 표시되는 이유는 무엇인가요?**

**A:** 의도적인 작동입니다. 디바이스는 클라우드의 리소스에 액세스할 수 없습니다. 

디바이스를 다시 등록하려면 디바이스에서 직접 작업을 수행해야 합니다. 

온-프레미스 AD 도메인에 가입된 Windows 10 및 Windows Server 2016에서 가입 상태를 지우려면

1.  관리자 권한으로 명령 프롬프트를 엽니다.

2.  `dsregcmd.exe /debug /leave`를 입력합니다.

3.  로그아웃했다가 로그인하여 디바이스를 Azure AD에 다시 등록하는 예약된 작업을 트리거합니다. 

온-프레미스 AD 도메인에 가입된 하위 수준 Windows OS 버전의 경우:

1.  관리자 권한으로 명령 프롬프트를 엽니다.
2.  `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`을 입력합니다.
3.  `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`을 입력합니다.

---

**Q: Azure Portal에서 중복된 디바이스 항목이 나타나는 이유는 무엇인가요?**

**A:**

-   Windows 10 및 Windows Server 2016의 경우 같은 디바이스를 반복해서 조인을 취소했다가 다시 조인하면 중복된 항목이 나타날 수 있습니다. 

-   회사 또는 학교 계정 추가를 사용한 경우 회사 또는 학교 계정 추가를 사용하는 각 Windows 사용자는 디바이스 이름이 같은 새 디바이스 레코드를 만들게 됩니다.

-   자동 등록을 사용하여 온-프레미스 AD 도메인에 가입된 하위 수준 Windows OS 버전을 디바이스에 로그인하는 각 도메인 사용자에 대해 디바이스 이름이 같은 새 디바이스 레코드를 만듭니다. 

-   초기화되었다가 같은 이름으로 다시 설치되고 다시 조인한 Azure AD 가입 머신은 디바이스 이름이 같은 다른 레코드로 표시됩니다.

---

**Q: Azure Portal에서 사용하지 않도록 설정한 디바이스에서 여전히 리소스에 액세스할 수 있는 이유는 무엇인가요?**

**A:** 해지가 적용되는 데 최대 1시간이 소요될 수 있습니다.

>[!Note] 
>등록된 디바이스의 경우에는 디바이스를 초기화하여 사용자가 리소스에 액세스하지 못하게 하는 것이 좋습니다. 자세한 내용은 [Intune에서 관리를 위해 디바이스 등록](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)을 참조하세요. 

---

## <a name="azure-ad-join-faq"></a>Azure AD 조인 FAQ

**Q: 디바이스에서 Azure AD 조인 디바이스를 로컬로 조인 해제하려면 어떻게 하나요?**

**A:** 
- 하이브리드 Azure AD 조인 디바이스의 경우, 예약된 작업이 디바이스를 다시 등록하지 않도록 자동 등록을 꺼야 합니다. 그런 다음, 관리자 권한으로 명령 프롬프트를 열고 `dsregcmd.exe /debug /leave`를 입력합니다. 또는 여러 디바이스에서 이 명령을 스크립트로 실행하여 대량으로 조인 해제할 수 있습니다.

- 순수한 Azure AD 조인 디바이스의 경우, Azure AD 사용자 자격 증명으로 로그인할 수 없으므로 오프라인 로컬 관리자 계정이 있는지 확인하거나 계정을 만들어야 합니다. 그런 다음, **설정** > **계정** > **회사 또는 학교 액세스**로 이동합니다. 계정을 선택하고 **연결 끊기**를 클릭합니다. 프롬프트를 따르고, 메시지가 표시되면 로컬 관리자 자격 증명을 제공합니다. 디바이스를 다시 부팅하여 조인 해제 프로세스를 완료합니다.

---

**Q: Azure AD에서 삭제 또는 비활성화된 Azure AD 조인 디바이스에 사용자가 로그인할 수 있나요?**
**A:** 예. Windows에서는 이전에 로그인한 사용자가 네트워크 연결 없이도 신속하게 데스크톱에 액세스할 수 있도록 로그온 기능이 캐시되었습니다. Azure AD에서 디바이스가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 로그온을 사용하여 데스크톱에 계속 액세스할 수 있습니다. 그러나 디바이스가 삭제 또는 비활성화되면 사용자는 디바이스 기반 조건부 액세스에 의해 보호되는 리소스에 액세스할 수 없습니다. 

아직 로그인하지 않은 사용자는 자신에게 설정된 캐시된 로그인이 없기 때문에 디바이스에 액세스할 수 없습니다. 

---

**Q: 비활성화되거나 삭제된 사용자가 Azure AD 조인 디바이스에 로그인할 수 있나요?**
**A:** 예, 하지만 시간 제한이 있습니다. Azure AD에서 사용자가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 즉시 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 로그온을 사용하여 데스크톱에 액세스할 수 있습니다. 디바이스가 사용자 상태를 인식하면(일반적으로 4시간 이하) Windows는 해당 사용자가 데스크톱에 액세스하지 못하도록 차단합니다. Azure AD에서 사용자가 삭제 또는 비활성화되면 해당 사용자의 모든 토큰이 취소되므로 어떤 리소스에도 액세스할 수 없습니다. 

이전에 로그인하지 않은 삭제 또는 비활성화된 사용자는 자신에게 설정된 캐시된 로그인이 없기 때문에 디바이스에 액세스할 수 없습니다. 

---

**Q: 사용자가 Azure AD 조인 디바이스에서 프린터를 검색할 수 없습니다. Azure AD 조인 디바이스에서 어떻게 인쇄를 사용하도록 설정할 수 있나요?**

**A:** Azure AD 조인 디바이스용 프린터를 배포하려면 [Hybrid cloud print](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)(하이브리드 클라우드 인쇄)를 참조하세요. 하이브리드 클라우드 인쇄를 배포하려면 온-프레미스 Windows Server가 필요합니다. 현재는 클라우드 기반 인쇄 서비스를 사용할 수 없습니다. 

---

**Q: 원격 Azure AD 조인 디바이스에 연결하려면 어떻게 하나요?**
**A:** 자세한 내용은 https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc 문서를 참조하세요.

---

**Q: 사용자에게 "여기에서 해당 위치로 이동할 수 없습니다." 메시지가 표시되는 이유는 무엇인가요?**

**A:** 특정 디바이스 상태를 요구하도록 특정 조건부 액세스 규칙을 구성했거나 디바이스가 조건을 충족하지 않을 경우 사용자가 차단되고 다음 메시지가 표시됩니다. 조건부 액세스 정책 규칙을 평가하고 디바이스가 이 조건을 충족하는지 확인하여 이 메시지가 표시되지 않도록 하세요.

---

**Q: 일부 사용자가 Azure AD 조인 디바이스에서 MFA 프롬프트를 확인하지 못하는 이유는 무엇인가요?**

**A:** 사용자가 다단계 인증을 사용하여 Azure AD에 디바이스를 조인하거나 등록하는 경우, 디바이스 자체는 해당 특정 사용자에 대한 신뢰할 수 있는 두 번째 요소가 됩니다. 이후에 동일한 사용자가 장치에 로그인하고 애플리케이션에 액세스할 때마다 Azure AD는 장치를 두 번째 요소로 간주하고 사용자가 추가 MFA 프롬프트 없이 애플리케이션에 원활하게 액세스할 수 있도록 합니다. 이 동작은 해당 장치에 로그인하는 다른 사용자에게 적용되지 않으므로, 해당 장치에 액세스하는 다른 모든 사용자에게 MFA가 필요한 애플리케이션에 액세스하기 전에 MFA 인증 질문이 표시됩니다.

---

**Q: Azure AD에 조인한 디바이스에 대해 “사용자 이름 또는 암호가 올바르지 않습니다.” 메시지가 표시되는 이유는 무엇인가요?**

**A:** 이 시나리오에 대한 일반적인 이유는 다음과 같습니다.

- 사용자 자격 증명이 더 이상 유효하지 않습니다.

- 컴퓨터가 Azure Active Directory와 통신할 수 없습니다. 네트워크 연결 문제가 있는지 확인합니다.

- 페더레이션 로그인을 수행하려면 페더레이션 서버가 WS-Trust 엔드포인트 사용을 지원하고 액세스 가능해야 합니다. 

- 통과 인증을 사용하도록 설정했으며, 사용자에게 로그온 시 변경해야 하는 임시 암호가 있습니다.

---

**Q: PC를 Azure AD에 가입하려고 할 때 “오류가 발생했습니다.” 대화 상자가 표시되는 이유는 무엇인가요?**

**A:** Intune에 Azure Active Directory를 등록했기 때문입니다. Azure AD 조인을 시도하는 사용자에게 올바른 Intune 라이선스가 할당되어 있는지 확인합니다. 자세한 내용은 [Windows 디바이스 관리 설정](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)을 참조하세요.  

---

**Q: 오류 정보가 표시되지 않더라도 PC에서 Azure AD 조인 시도가 실패하는 이유는 무엇인가요?**

**A:** 한 가지 가능한 원인은 사용자가 기본 제공 로컬 관리자 계정을 사용하여 디바이스에 로그인했기 때문입니다. Azure Active Directory 조인을 사용하여 설치를 완료하기 전에 다른 로컬 계정을 만듭니다. 

---

## <a name="hybrid-azure-ad-join-faq"></a>하이브리드 Azure AD 조인 FAQ

**Q: 하이브리드 Azure AD 조인 실패를 진단하기 위한 문제 해결 정보는 어디에서 찾을 수 있나요?**

**A:** 문제 해결 정보는 다음을 참조하세요.

- [Windows 10 및 Windows Server 2016에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](troubleshoot-hybrid-join-windows-current.md)

- [Windows 하위 수준 클라이언트에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)
 
**Q: Azure AD 디바이스 목록에서 내 Windows 10 하이브리드 Azure AD 가입 디바이스에 대한 Azure AD 등록 레코드가 중복해서 표시되는 이유는 무엇입니까?**

**A:** 사용자가 도메인 가입 디바이스의 앱에 계정을 추가하면 "Windows에 계정을 추가하시겠습니까?"라는 메시지가 표시될 수 있습니다. 메시지에서 "예"를 클릭하면 디바이스가 Azure AD에 등록되고, 신뢰 유형이 Azure AD 등록으로 표시될 수 있습니다. 조직에서 하이브리드 Azure AD 가입을 사용하도록 설정하면 디바이스도 하이브리드 Azure AD에 가입됩니다. 따라서 동일한 디바이스에 두 가지 디바이스 상태가 표시됩니다. 그러나 하이브리드 Azure AD 가입이 Azure AD 등록 상태보다 우선합니다. 따라서 모든 인증 및 조건부 액세스 평가에서 디바이스가 하이브리드 Azure AD 가입 상태로 간주됩니다. 그러므로 Azure AD 포털에서 Azure AD 등록 디바이스 레코드를 삭제해도 무방합니다. Windows 10 머신에서 이러한 이중 상태를 방지하거나 정리하는 방법을 이해하려면 하이브리드 Azure AD 가입 문서의 [이 섹션](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know)을 살펴보세요. 

---

## <a name="azure-ad-register-faq"></a>Azure AD 등록 FAQ

**Q: Android 또는 iOS BYOD 디바이스를 등록할 수 있나요?**

**A:** 예, 하이브리드 고객만 Azure 디바이스 등록 서비스를 사용하여 등록할 수 있습니다. AD FS의 온-프레미스 디바이스 등록 서비스에서는 지원되지 않습니다.

**Q: macOS 디바이스를 등록하려면 어떻게 하나요?**

**A:** macOS 디바이스를 등록하려면 다음을 수행합니다.

1.  [규정 준수 정책 만들기](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [macOS 디바이스에 대한 조건부 액세스 정책 정의](../active-directory-conditional-access-azure-portal.md) 

**설명**

- 조건부 액세스 정책에 포함된 사용자는 리소스에 액세스할 수 있는 [macOS에 대해 지원되는 버전의 Office](../conditional-access/technical-reference.md#client-apps-condition)가 필요합니다. 

- 첫 번째 액세스를 시도하는 동안 사용자가 회사 포털을 사용하여 디바이스를 등록하라는 메시지가 표시됩니다.

---
