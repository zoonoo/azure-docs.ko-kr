---
title: Azure Active Directory 디바이스 관리 FAQ | Microsoft Docs
description: Azure Active Directory 디바이스 관리 FAQ
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 513b1d7468700076ae4d3fd46284ef88d5f28c51
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296177"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 디바이스 관리 FAQ

**Q: 최근에 디바이스를 등록했습니다. Azure Portal에서 내 사용자 정보에 디바이스가 표시되지 않는 이유는 무엇인가요? 또는 하이브리드 Azure AD(Azure Active Directory) 조인 디바이스의 디바이스 소유자가 N/A로 표시되는 이유는 무엇인가요?**

**A:** 하이브리드 Azure AD 조인 Windows 10 디바이스는 **사용자 디바이스** 아래에 표시되지 않습니다.
Azure Portal에서 **모든 디바이스** 보기를 사용하세요. PowerShell [Get-MsolDevice](https://docs.microsoft.com/en-us/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet을 사용할 수도 있습니다.

다음 디바이스만 **사용자 디바이스** 아래에 나열됩니다.

- 하이브리드 Azure AD에 조인되지 않은 모든 개인 디바이스 
- 모든 비 Windows 10 또는 Windows Server 2016 디바이스
- 모든 비 Windows 디바이스 

--- 

**Q: 클라이언트의 디바이스 등록 상태를 어떻게 알 수 있나요?**

**A:** Azure portal에서 **모든 디바이스**로 이동합니다. 디바이스 ID를 사용하여 디바이스를 검색합니다. 조인 유형 열 아래의 값을 확인합니다. 경우에 따라 디바이스가 다시 설정되거나 다시 설치될 수 있습니다. 따라서 디바이스에서도 디바이스 등록 상태를 반드시 확인해야 합니다.

- Windows 10 및 Windows Server 2016 이상 디바이스인 경우 `dsregcmd.exe /status` 명령을 실행합니다.
- 하위 수준 OS 버전인 경우 `%programFiles%\Microsoft Workplace Join\autoworkplace.exe` 명령을 실행합니다.

---

**Q: Azure Portal의 사용자 정보에 디바이스 레코드가 보입니다. 그리고 디바이스에서 상태가 등록됨으로 표시됩니다. 조건부 액세스를 사용하기 위한 설정이 올바르게 된 것인가요?**

**A:** **deviceID**로 표시된 디바이스 가입 상태는 Azure AD의 상태와 일치해야 하며 조건부 액세스에 대한 평가 조건을 만족해야 합니다. 자세한 내용은 [조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 관리 디바이스 필요](../conditional-access/require-managed-devices.md)를 참조하세요.

---

**Q: Azure Portal에서 또는 Windows PowerShell을 사용하여 디바이스를 삭제했습니다. 하지만 디바이스의 로컬 상태는 여전히 등록된 것으로 표시됩니다.**

**A:** 이 작업은 의도된 것입니다. 디바이스는 클라우드의 리소스에 액세스할 수 없습니다. 

다시 등록하려면 디바이스에서 수동 작업을 수행해야 합니다. 

온-프레미스 Active Directory 도메인에 조인된 Windows 10 및 Windows Server 2016에서 조인 상태를 지우려면 다음 단계를 수행합니다.

1.  관리자 권한으로 명령 프롬프트를 엽니다.

2.  [https://slack.botframework.com](`dsregcmd.exe /debug /leave`) 을 입력합니다.

3.  로그아웃했다가 다시 로그인하여 디바이스를 Azure AD에 다시 등록하는 예약된 작업을 트리거합니다. 

온-프레미스 Active Directory 도메인에 조인된 하위 수준 Windows OS 버전의 경우 다음 단계를 수행합니다.

1.  관리자 권한으로 명령 프롬프트를 엽니다.
2.  [https://slack.botframework.com](`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`) 을 입력합니다.
3.  [https://slack.botframework.com](`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`) 을 입력합니다.

---

**Q: Azure Portal에서 중복된 디바이스 항목이 나타나는 이유는 무엇인가요?**

**A:**

-   Windows 10 및 Windows Server 2016의 경우 동일한 디바이스에 조인 취소 및 다시 조인을 반복적으로 시도하면 중복된 항목이 발생할 수 있습니다. 

-   **회사 또는 학교 계정 추가**를 사용하는 각 Windows 사용자는 디바이스 이름이 같은 새 디바이스 레코드를 만듭니다.

-   온-프레미스 Azure Directory 도메인에 조인된 하위 수준 Windows OS 버전의 경우 자동 등록에 의해 디바이스에 로그인하는 각 도메인 사용자의 디바이스와 이름이 같은 새 디바이스 레코드가 생성됩니다. 

-   초기화되었다가 같은 이름으로 다시 설치되고 다시 조인된 Azure AD 조인 머신은 디바이스 이름이 같은 다른 레코드로 표시됩니다.

---

**Q: Azure Portal에서 사용하지 않도록 설정한 디바이스에서 여전히 리소스에 액세스할 수 있는 이유는 무엇인가요?**

**A:** 해지가 적용되는 데 최대 1시간이 소요될 수 있습니다.

>[!NOTE] 
>등록된 디바이스의 경우 사용자가 리소스에 액세스할 수 없도록 디바이스를 초기화하는 것이 좋습니다. 자세한 내용은 [디바이스 등록이란?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)을 참조하세요. 

---

## <a name="azure-ad-join-faq"></a>Azure AD 조인 FAQ

**Q: 디바이스에서 Azure AD 조인 디바이스를 로컬로 조인 해제하려면 어떻게 하나요?**

**A:** 
- 하이브리드 Azure AD 조인 디바이스의 경우 자동 등록을 해제해야 합니다. 그러면 예약된 작업에서 디바이스를 다시 등록하지 않습니다. 다음으로, 관리자 권한으로 명령 프롬프트를 열고 `dsregcmd.exe /debug /leave`를 입력합니다. 또는 여러 디바이스에서 이 명령을 스크립트로 실행하여 대량으로 조인 취소합니다.

- 순수 Azure AD 조인 디바이스의 경우 오프라인 로컬 관리자 계정이 있어야 하며, 없으면 만들어야 합니다. Azure AD 사용자 자격 증명으로 로그인할 수 없습니다. 그런 다음, **설정** > **계정** > **회사 또는 학교 액세스**로 이동합니다. 계정을 선택하고 **연결 끊기**를 선택합니다. 프롬프트를 따르고, 메시지가 표시되면 로컬 관리자 자격 증명을 제공합니다. 디바이스를 다시 부팅하여 조인 취소 프로세스를 완료합니다.

---

**Q: Azure AD에서 삭제 또는 비활성화된 Azure AD 조인 디바이스에 사용자가 로그인할 수 있나요?**

**A:** 예. Windows에는 이전에 로그인한 사용자가 네트워크 연결 없이도 신속하게 데스크톱에 액세스할 수 있게 해주는 캐시된 사용자 이름 및 암호 기능이 있습니다. 

Azure AD에서 디바이스가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 사용자 이름 및 암호를 사용하여 데스크톱에 계속 액세스할 수 있습니다. 그러나 디바이스가 삭제 또는 비활성화되면 사용자는 디바이스 기반 조건부 액세스에 의해 보호되는 리소스에 액세스할 수 없습니다. 

이전에 로그인하지 않은 사용자는 디바이스에 액세스할 수 없습니다. 이들에게 활성화된 캐시된 사용자 이름 및 암호가 없기 때문입니다. 

---

**Q: 비활성화되거나 삭제된 사용자가 Azure AD 조인 디바이스에 로그인할 수 있나요?**

**A:** 예, 하지만 시간 제한이 있습니다. Azure AD에서 사용자가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 즉시 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 사용자 이름 및 암호를 사용하여 데스크톱에 액세스할 수 있습니다. 

일반적으로 디바이스는 4시간 이내에 사용자 상태를 인식합니다. 그 후 Windows가 데스크톱에 대한 사용자 액세스를 차단합니다. Azure AD에서 사용자가 삭제 또는 비활성화되면 해당 사용자의 모든 토큰이 취소됩니다. 따라서 리소스에 액세스할 수 없습니다. 

이전에 로그인하지 않았으며 삭제 또는 비활성화된 사용자는 디바이스에 액세스할 수 없습니다. 이들에게 활성화된 캐시된 사용자 이름 및 암호가 없기 때문입니다. 

---

**Q: Azure AD 조인 디바이스에서 UPN을 변경한 사용자에게 문제가 발생하는 이유는 무엇인가요?**

**A:** 현재 Azure AD 조인 디바이스에서는 UPN이 완전히 지원되지 않습니다. 그러므로 이러한 디바이스에서 UPN을 변경하고 나면 Azure AD 인증이 실패합니다. 그러면 사용자의 디바이스에서 SSO 및 조건부 액세스 문제가 발생합니다. 이 경우 문제를 해결하려면 사용자가 새 UPN을 사용하여 "다른 사용자" 타일을 통해 Windows에 로그인해야 합니다. 현재 이 문제를 해결하기 위한 작업이 진행되고 있습니다. 하지만 비즈니스용 Windows Hello를 통해 로그인하는 사용자에게는 이 문제가 발생하지 않습니다. 

---

**Q: 사용자가 Azure AD 조인 디바이스에서 프린터를 검색할 수 없습니다. 이러한 디바이스에서 인쇄를 사용하려면 어떻게 해야 하나요?**

**A:** Azure AD 조인 디바이스용 프린터를 배포하려면 [사전 인증을 사용하여 Windows Server 하이브리드 클라우드 인쇄 배포](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)를 참조하세요. 하이브리드 클라우드 인쇄를 배포하려면 온-프레미스 Windows Server가 필요합니다. 현재는 클라우드 기반 인쇄 서비스를 사용할 수 없습니다. 

---

**Q: 원격 Azure AD 조인 디바이스에 연결하려면 어떻게 할까요?**

**A:** [원격 Azure Active Directory 조인 PC에 연결](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)을 참조하세요.

---

**Q: 사용자에게 *여기에서 해당 위치로 이동할 수 없습니다* 메시지가 표시되는 이유는 무엇인가요?**

**A:** 특정 디바이스 상태를 요구하도록 특정 조건부 액세스 규칙을 구성했나요? 디바이스가 조건을 충족하지 않으면 사용자가 차단되고 해당 메시지가 표시됩니다. 조건부 액세스 정책 규칙을 평가합니다. 이 메시지가 표시되지 않게 하려면 디바이스가 조건을 충족해야 합니다.

---

**Q: Azure AD 조인 디바이스에서 일부 사용자에게 Azure Multi-Factor Authentication 프롬프트가 표시되는 이유는 무엇인가요?**

**A:** 사용자가 Multi-Factor Authentication을 사용하여 디바이스를 Azure AD에 조인 또는 등록할 수 있습니다. 그러면 디바이스 자체가 해당 사용자의 신뢰할 수 있는 두 번째 요소가 됩니다. 동일한 사용자가 디바이스에 로그인하여 애플리케이션에 액세스할 때마다 Azure AD는 디바이스를 두 번째 요소로 간주합니다. 해당 사용자가 추가 Multi-Factor Authentication 프롬프트 없이 원활하게 애플리케이션에 액세스할 수 있게 해줍니다. 

이 동작은 해당 디바이스에 로그인하는 다른 사용자에게 적용되지 않습니다. 따라서 해당 디바이스에 액세스하는 다른 모든 사용자에게 Multi-Factor Authentication이 요구됩니다. 그 후 Multi-Factor Authentication을 요구하는 애플리케이션에 액세스할 수 있습니다.

---

**Q: Azure AD에 조인한 디바이스에 대해 *사용자 이름 또는 암호가 올바르지 않습니다* 메시지가 표시되는 이유는 무엇인가요?**

**A:** 이 시나리오에 대한 일반적인 이유는 다음과 같습니다.

- 사용자 자격 증명이 더 이상 유효하지 않습니다.

- 컴퓨터가 Azure Active Directory와 통신할 수 없습니다. 네트워크 연결 문제가 있는지 확인합니다.

- 페더레이션 로그인을 수행하려면 사용하도록 설정되어 있고 액세스 가능한 WS-Trust 엔드포인트를 페더레이션 서버에서 지원해야 합니다. 

- 통과 인증을 사용하도록 설정했습니다. 따라서 로그인할 때 임시 암호를 변경해야 합니다.

---

**Q: PC를 Azure AD에 조인하려고 할 때 *오류가 발생했습니다* 대화 상자가 표시되는 이유는 무엇인가요?**

**A:** 이 오류는 Intune을 사용하여 Azure Active Directory 등록을 설정한 경우에 발생합니다. Azure AD 조인을 시도한 사용자에게 올바른 Intune 라이선스가 할당되어야 합니다. 자세한 내용은 [Windows 디바이스에 대한 등록 설정](https://docs.microsoft.com/intune/windows-enroll#azure-active-directory-enrollment)을 참조하세요.  

---

**Q: 오류 정보가 표시되지 않더라도 PC에서 Azure AD 조인 시도가 실패하는 이유는 무엇인가요?**

**A:** 한 가지 가능한 원인은 사용자가 기본 제공 로컬 관리자 계정을 사용하여 디바이스에 로그인했기 때문입니다. Azure Active Directory 조인을 사용하여 설치를 완료하기 전에 다른 로컬 계정을 만드세요. 

---

**Q: Windows 10 디바이스에 있는 MS-Organization-P2P-Access 인증서는 무엇인가요?**

**A:** MS-Organization-P2P-Access 인증서는 Azure AD를 통해 Azure AD 조인 디바이스와 하이브리드 Azure AD 조인 디바이스에 모두 발급됩니다. 이러한 인증서는 원격 데스크톱 시나리오에 대한 동일한 테넌트에 있는 디바이스 간 트러스트를 설정하는 데 사용됩니다. 한 인증서는 디바이스에 발급되고 다른 인증서는 사용자에게 발급됩니다. 디바이스 인증서는 `Local Computer\Personal\Certificates`에 있으며 하루 동안 유효합니다. 디바이스가 Azure AD에서도 활성 상태이면 (새 인증서를 발급하여) 이 인증서가 갱신됩니다. 사용자 인증서는 `Current User\Personal\Certificates`에 있으며 마찬가지로 하루 동안 유효하지만, 사용자가 다른 Azure AD 조인 디바이스에 대한 원격 데스크톱 세션을 시도할 때 주문형으로 발급됩니다. 만료 시 갱신되지 않습니다. 두 인증서는 `Local Computer\AAD Token Issuer\Certificates`에 있는 MS-Organization-P2P-Access 인증서를 사용하여 발급됩니다. 이 인증서는 디바이스를 등록할 때 Azure AD에서 발급합니다. 

---

**Q: MS-Organization-P2P-Access를 통해 발급되어 기간이 만료된 여러 인증서가 Windows 10 디바이스에 표시되는 이유는 무엇인가요? 어떻게 삭제할 수 있나요?**

**A:** Windows 10 1709 이하 버전에서 암호화 문제로 인해 만료된 MS-Organization-P2P-Access 인증서가 컴퓨터 저장소에 계속 남아 있는 문제가 확인되었습니다. 만료된 인증서를 대규모로 처리할 수 없는 VPN 클라이언트(예: Cisco AnyConnect)를 사용하는 경우 고객이 네트워크 연결 문제를 겪을 수 있습니다. 이 문제는 만료된 MS-Organization-P2P-Access 인증서를 자동으로 삭제하는 방법을 사용하여 Windows 10 1803 릴리스에서 해결되었습니다. 디바이스를 Windows 10 1803으로 업데이트하여 이 문제를 해결할 수 있습니다. 업데이트할 수 없는 경우 악영향 없이 이러한 인증서를 삭제할 수 있습니다.  

---


## <a name="hybrid-azure-ad-join-faq"></a>하이브리드 Azure AD 조인 FAQ

**Q: 하이브리드 Azure AD 조인 실패를 진단하기 위한 문제 해결 정보는 어디에서 찾을 수 있나요?**

**A:** 문제 해결 정보는 다음 문서를 참조하세요.

- [Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md)

- [하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)
 
**Q: Azure AD 디바이스 목록에서 내 Windows 10 하이브리드 Azure AD 가입 디바이스에 대한 Azure AD 등록 레코드가 중복해서 표시되는 이유는 무엇입니까?**

**A:** 사용자가 도메인 조인 디바이스의 앱에 계정을 추가하면 **Windows에 계정을 추가하시겠습니까?** 라는 메시지가 표시될 수 있습니다. 사용자가 프롬프트에서 **예**를 입력하면 디바이스가 Azure AD에 등록됩니다. 신뢰 유형은 Azure AD 등록으로 표시됩니다. 조직에서 하이브리드 Azure AD 조인을 사용하도록 설정하면 디바이스도 하이브리드 Azure AD에 조인됩니다. 동일한 디바이스에 대한 두 가지 디바이스 상태가 표시됩니다. 

하이브리드 Azure AD 조인이 Azure AD 등록 상태보다 우선합니다. 따라서 모든 인증 및 조건부 액세스 평가에서 디바이스가 하이브리드 Azure AD 조인 상태로 간주됩니다. Azure AD 포털에서 Azure AD 등록 디바이스 레코드를 삭제해도 무방합니다. [Windows 10 머신에서 이 이중 상태를 피하는 방법 또는 정리하는 방법](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know)을 알아보세요. 


---

**Q: Windows 10 하이브리드 Azure AD 조인 디바이스에서 UPN을 변경한 사용자에게 문제가 발생하는 이유는 무엇인가요?**

**A:** 현재 하이브리드 Azure AD 조인 디바이스에서는 UPN이 완전히 지원되지 않습니다. 사용자는 디바이스에 로그인하여 온-프레미스 애플리케이션에 액세스할 수는 있지만 UPN을 변경하고 나면 Azure AD 인증이 실패합니다. 그러면 사용자의 디바이스에서 SSO 및 조건부 액세스 문제가 발생합니다. 이 경우 문제를 해결하려면 Azure AD에서 디바이스 조인을 취소한 다음(상승된 권한으로 "dsregcmd /leave" 실행) 다시 조인해야 합니다(자동으로 진행됨). 현재 이 문제를 해결하기 위한 작업이 진행되고 있습니다. 하지만 비즈니스용 Windows Hello를 통해 로그인하는 사용자에게는 이 문제가 발생하지 않습니다. 

---


## <a name="azure-ad-register-faq"></a>Azure AD 등록 FAQ

**Q: Android 또는 iOS BYOD 디바이스를 등록할 수 있나요?**

**A:** 예, 하이브리드 고객만 Azure 디바이스 등록 서비스를 사용하여 등록할 수 있습니다. AD FS(Active Directory Federation Services)의 온-프레미스 디바이스 등록 서비스에는 지원되지 않습니다.

**Q: macOS 디바이스를 등록하려면 어떻게 하나요?**

**A:** 다음과 같은 단계를 수행합니다.

1.  [규정 준수 정책 만들기](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [macOS 디바이스에 대한 조건부 액세스 정책 정의](../active-directory-conditional-access-azure-portal.md) 

**설명**

- 조건부 액세스 정책에 포함된 사용자는 리소스에 액세스할 수 있는 [macOS에 대해 지원되는 버전의 Office](../conditional-access/technical-reference.md#client-apps-condition)가 필요합니다. 

- 첫 번째 액세스를 시도하는 동안 사용자에게는 회사 포털을 사용하여 디바이스를 등록하라는 메시지가 표시됩니다.

