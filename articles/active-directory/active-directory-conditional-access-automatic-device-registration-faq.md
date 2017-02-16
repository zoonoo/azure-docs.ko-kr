---
title: "자동 장치 등록 FAQ | Microsoft Docs"
description: "Azure Active Directory를 사용한 장치 등록과 관련된 FAQ입니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 918c2b096b9b6d935c8d5bc2588a978cdf8878cf
ms.openlocfilehash: 0c4f1489893f464eee1f76e94f2b35650f06e44e


---
# <a name="automatic-device-registration-faq"></a>자동 장치 등록 FAQ

**Q: 최근에 장치를 등록했습니다. Azure Portal에서 내 사용자 정보에 장치가 표시되지 않는 이유는 무엇인가요?**

**A:** 자동 장치 등록이 지정된 도메인 연결 Windows 10 장치가 사용자 정보 아래에 표시되지 않습니다.
모든 장치를 표시하려면 PowerShell을 사용해야 합니다. 

다음 장치만 사용자 정보 아래에 나열됩니다.

- 엔터프라이즈에 조인되지 않은 모든 개인 장치 
- 모든 비 Windows 10/Windows Server 2016 
- 모든 비 Windows 장치 

---

**Q: Azure Portal에서 Azure Active Directory에 등록된 모든 장치를 볼 수는 없는 이유는 무엇인가요?** 

**A:** 현재 방법이 Azure Portal에서 등록된 모든 장치를 볼 수 있는 방법은 없습니다. 모든 장치를 찾으려면 Azure PowerShell을 사용할 수 있습니다. 자세한 내용은 [Get-MsolDevice](https://docs.microsoft.com/en-us/powershell/msonline/v1/get-msoldevice) cmdlet을 참조하세요.

--- 

**Q: 클라이언트의 장치 등록 상태를 어떻게 알 수 있나요?**

**A:** 장치 등록 상태는 다음에 따라 다릅니다.

- 장치가 무엇인지
- 등록된 방법 
- 관련된 모든 세부 정보 
 

---

**Q: Azure Portal에서 또는 Windows PowerShell을 사용하여 삭제한 장치가 여전히 등록된 것으로 표시되는 이유는 무엇인가요?**

**A:** 이것은 의도적인 작동입니다. 장치는 클라우드의 리소스에 액세스할 수 없습니다. 장치를 다시 등록하려면 장치에 대해 수동 작업을 수행해야 합니다. 

온-프레미스 AD 도메인에 조인된 Windows 10 및 Windows Server 2016:

1.  관리자 권한으로 명령 프롬프트를 엽니다.

2.  **dsregcmd.exe /leave**를 입력합니다.

3.  **dsregcmd.exe**를 입력합니다.

온-프레미스 AD 도메인에 조인된 다른 Windows 플랫폼:

1.  관리자 권한으로 명령 프롬프트를 엽니다.
2.  `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`을 입력합니다.
3.  `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`을 입력합니다.

---

**Q: Azure Portal에서 중복된 장치 항목이 나타나는 이유는 무엇인가요?**

**A:**

-   Windows 10 및 Windows Server 2016의 경우 같은 장치를 반복해서 조인 해제했다가 다시 조인하면 중복된 항목이 나타날 수 있습니다. 

-   회사 또는 학교 계정 추가를 사용한 경우 회사 또는 학교 계정 추가를 사용하는 각 Windows 사용자는 장치 이름이 같은 새 장치 레코드를 만들게 됩니다.

-   자동 등록을 사용하여 온-프레미스 AD 도메인에 조인된 다른 Windows 플랫폼은 장치에 로그인하는 각 도메인 사용자에 대해 장치 이름이 같은 새 장치 레코드를 만듭니다. 

-   초기화되었다가 같은 이름으로 다시 설치되고 다시 조인된 AADJ 장치는 장치 이름이 같은 다른 레코드로 표시됩니다.

---

**Q: Azure Portal에서 사용하지 않도록 설정한 장치에서 여전히 리소스에 액세스할 수 있는 이유는 무엇인가요?**

**A:** 해지가 적용되는 데는 최대&1;시간이 소요될 수 있습니다.

>[!Note] 
>분실한 장치의 경우에는 장치를 초기화하여 사용자가 장치에 액세스하지 못하게 하는 것이 좋습니다. 자세한 내용은 [Intune에서 관리를 위해 장치 등록](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)을 참조하세요. 


---

**Q: 사용자에게 "여기에서 해당 위치로 이동할 수 없습니다." 메시지가 표시되는 이유는 무엇인가요?**

**A:** 특정 장치 상태를 요구하도록 특정 조건부 액세스 규칙을 구성했거나 장치가 조건을 충족하지 않을 경우 사용자가 차단되고 다음 메시지가 표시됩니다. 규칙을 평가하고 장치가 이 조건을 충족하는지 확인하여 이 메시지가 표시되지 않도록 하세요.

---


**Q: Azure Portal에서 사용자 정보에 장치 레코드가 표시되고 클라이언트에 등록된 상태로 표시됩니다. 조건부 액세스를 사용할 수 있게 제대로 설정되어 있는 것인가요?**

**A:** Azure Portal의 장치 레코드(deviceID) 및 상태가 클라이언트와 일치하고 조건부 액세스를 위한 평가 조건을 충족해야 합니다. 자세한 내용은 [Azure Active Directory Device 등록 시작](active-directory-conditional-access-device-registration-overview.md)을 참조하세요.

---

**Q: Azure AD에 조인한 장치에 대해 "사용자 이름 또는 암호가 올바르지 않습니다." 메시지가 표시되는 이유는 무엇인가요?**

**A:** 이 시나리오에 대한 일반적인 이유는 다음과 같습니다.

1.  사용자 자격 증명이 더 이상 유효하지 않습니다.

2.  컴퓨터가 Azure Active Directory와 통신할 수 없습니다. 네트워크 연결 문제가 있는지 확인합니다.

3.  Azure AD 조인 필수 구성 요소가 충족되지 않았습니다. [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-overview.md)에 대한 단계를 따라야 합니다.  

4.  페더레이션 로그인을 수행하려면 페더레이션 서버가 Ws-Trust 활성 끝점을 지원해야 합니다. 

---

**Q: PC를 조인하려고 할 때 "오류가 발생했습니다." 대화 상자가 표시되는 이유는 무엇인가요?**

**A:** Intune에 Azure Active Directory를 등록했기 때문입니다. 자세한 내용은 [Windows 장치 관리 설정](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)을 참조하세요.  

---

**Q: 오류 정보가 표시되지 않더라도 PC 조인 시도가 실패하는 이유는 무엇인가요?**

**A:** 한 가지 가능한 원인은 사용자가 기본 제공 관리자 계정을 사용하여 장치에 로그인했기 때문입니다. Azure Active Directory 조인을 사용하여 설치를 완료하기 전에 다른 로컬 계정을 만드세요. 

---

**Q: 자동 장치 등록에 대한 문제 해결 정보는 어디에서 찾나요?**

**A:** 문제 해결 정보는 다음을 참조하세요.

1. [Windows 10 및 Windows Server 2016에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md)

2. [Windows 하위 수준 클라이언트에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy.md)
 
---




<!--HONumber=Feb17_HO1-->


