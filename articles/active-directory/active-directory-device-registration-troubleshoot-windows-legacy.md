---
title: "Windows 하위 수준 클라이언트에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결 | Microsoft Docs"
description: "Windows 하위 수준 클라이언트에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결"
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
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 49af838d750ba61694bdb28a83ee6e531627527d
ms.contentlocale: ko-kr
ms.lasthandoff: 03/10/2017

---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad-for-windows-down-level-clients"></a>Windows 하위 수준 클라이언트에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결 

이 항목은 다음 클라이언트에만 적용됩니다. 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 또는 Windows Server 2016의 경우 [Windows 10 및 Windows Server 2016에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](active-directory-device-registration-troubleshoot-windows.md)을 참조하세요.

이 항목에서는 [Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법](active-directory-device-registration-get-started.md)에 설명된 대로 도메인 조인 장치의 자동 등록을 구성했다고 가정합니다.
 
이 항목에서는 잠재적인 문제를 해결하는 방법에 대한 문제 해결 지침을 제공합니다.  
성공적인 결과를 얻기 위해 알아두어야 할 사항: 

- Azure AD에서 이러한 클라이언트는 사용자/장치 기준으로 등록됩니다. 예제: jdoe 및 jharnett이 이 장치에 로그인하는 경우 사용자 정보 탭에 해당 사용자에 대해 별도 등록(DeviceID)이 만들어집니다.  

- 이러한 클라이언트의 등록은 기본적으로 로그온 또는 잠금/잠금 해제 시 시도되도록 구성되어 있으며 작업 스케줄러 작업을 사용하여 5분 지연된 후에 트리거되도록 할 수 있습니다. 

- 운영 체제의 다시 설치나 수동 등록 취소 및 다시 등록이 수행되면 Azure AD에서 새 등록이 생성될 수 있으며 Azure Portal의 사용자 정보 탭에 여러 항목이 생성됩니다. 


## <a name="step-1-retrieve-the-registration-status"></a>1단계: 등록 상태 검색 

**장치 등록 상태를 확인하려면**  

1. 관리자 권한으로 명령 프롬프트를 엽니다. 

2. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`를 입력합니다.

이 명령을 실행하면 조인 상태에 대한 자세한 세부 정보를 제공하는 대화 상자가 표시됩니다.

![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>2단계: 등록 상태 평가 

조인이 실패하는 경우 이 대화 상자에는 발생한 문제에 대한 세부 정보가 표시됩니다.

**가장 일반적인 문제는 다음과 같습니다.**

- 잘못 구성된 AD FS 또는 Azure AD

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- 도메인 사용자로 로그온되지 않음

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- 할당량에 도달함

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- 서비스가 응답하지 않음 

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

**Applications and Services Log\Microsoft-Workplace Join**의 이벤트 로그에서 상태 정보를 찾을 수도 있습니다.
  
**실패한 등록에 대한 가장 일반적인 원인은 다음과 같습니다.** 

- 컴퓨터가 조직의 내부 네트워크에 있지 않거나 온-프레미스 AD 도메인 컨트롤러에 연결되지 않은 VPN에 있습니다.

- 로컬 컴퓨터 계정으로 컴퓨터에 로그온됩니다. 

- 서비스 구성 문제: 

  - 페더레이션 서버가 **WIAORMULTIAUTHN**을 지원하도록 구성되었습니다. 

  - 컴퓨터가 속한 AD 포리스트의 Azure AD에서 확인된 도메인 이름을 가리키는 서비스 연결 지점 개체가 없습니다.

  - 장치 제한에 도달했습니다. Azure Active Directory 장치 등록 시작을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [자동 장치 등록 FAQ](active-directory-device-registration-faq.md)를 참조하세요. 

