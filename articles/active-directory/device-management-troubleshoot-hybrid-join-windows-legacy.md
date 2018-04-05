---
title: 하위 수준 장치에 조인된 하이브리드 Azure Active Directory 문제 해결 | Microsoft Docs
description: 하위 수준 장치에 조인된 하이브리드 Azure Active Directory 문제 해결
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0d21a8848222c4b09723e22d2d51ec43b2154553
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>하위 수준 장치에 조인된 하이브리드 Azure Active Directory 문제 해결 

이 항목은 다음 장치에만 적용됩니다. 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 또는 Windows Server 2016의 경우 [Windows 10 및 Windows Server 2016 장치에 조인된 하이브리드 Azure Active Directory 문제 해결](device-management-troubleshoot-hybrid-join-windows-current.md)을 참조하세요.

이 항목에서는 다음 시나리오를 지원하도록 [장치에 조인된 하이브리드 Azure Active Directory를 구성](device-management-hybrid-azuread-joined-devices-setup.md)했다고 가정합니다.

- 장치 기반 조건부 액세스

- [엔터프라이즈 설정 로밍](active-directory-windows-enterprise-state-roaming-overview.md)

- [비즈니스용 Windows Hello](active-directory-azureadjoin-passport-deployment.md) 





이 항목에서는 잠재적인 문제를 해결하는 방법에 대한 문제 해결 지침을 제공합니다.  

**알아야 할 사항:** 

- 사용자당 최대 장치 수는 장치 중심입니다. 예를 들어 *jdoe* 및 *jharnett*가 장치에 로그인하는 경우 **사용자** 정보 탭에 각각에 대해 별도 등록(DeviceID)이 만들어집니다.  

- 초기 등록 / 장치 조인은 로그온 또는 잠금 / 잠금 해제 시 시도를 수행하도록 구성됩니다. 작업 스케줄러 작업에 의해 트리거되는 5분 지연이 있을 수 있습니다. 

- 운영 체제의 다시 설치나 수동 등록 취소 및 다시 등록이 수행되면 Azure AD에서 새 등록이 생성될 수 있으며 Azure Portal의 사용자 정보 탭에 여러 항목이 생성됩니다. 

## <a name="step-1-retrieve-the-registration-status"></a>1단계: 등록 상태 검색 

**장치 등록 상태를 확인하려면**  

1. 관리자 권한으로 명령 프롬프트를 엽니다. 

2. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`를 입력합니다.

이 명령을 실행하면 조인 상태에 대한 자세한 세부 정보를 제공하는 대화 상자가 표시됩니다.

![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>2단계: 하이브리드 Azure AD 조인 상태 평가 

하이브리드 Azure AD 조인이 실패하는 경우 이 대화 상자에는 발생한 문제에 대한 세부 정보가 표시됩니다.

**가장 일반적인 문제는 다음과 같습니다.**

- 잘못 구성된 AD FS 또는 Azure AD

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- 도메인 사용자로 로그온되지 않음

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    발생할 수 있는 몇 가지 다른 이유가 있습니다.
    
    1. 로그인한 사용자가 도메인 사용자(예: 로컬 사용자)가 아닌 경우 도메인 사용자에 대해 하위 수준 장치의 하이브리드 Azure AD 조인만 지원됩니다.
    
    2. 어떤 이유로든 Autoworkplace.exe가 Azure AD 또는 AD FS를 사용하여 자동으로 인증할 수 없는 경우 몇 가지 가능한 원인은 Azure AD URL에 대한 아웃바운드 네트워크 연결 문제(필수 구성 요소 확인) 또는 MFA가 사용자에 대해 활성화/구성되었는지 여부일 수 있습니다. 그러나 WIAORMUTLIAUTHN이 페더레이션 서버에서 구성되지 않았습니다(구성 단계 확인). 또 다른 가능성은 HRD(홈 영역 검색) 페이지가 사용자 상호 작용을 기다리고 Autoworkplace.exe가 자동으로 토큰을 가져오지 못하게 하는 경우입니다.
    
    3. 조직에서 Azure AD 원활한 Single Sign-On을 사용하고 있는 경우 다음 URL은 장치의 IE 인트라넷 설정에 없습니다.
    
       - https://autologon.microsoftazuread-sso.com

    
       그리고 "스크립트를 통해 상태 표시줄에 업데이트 허용" 설정은 인트라넷 영역에 대해 활성화되어야 합니다.

- 할당량에 도달함

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- 서비스가 응답하지 않음 

    ![Windows에 대한 작업 공간 연결](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

**Applications and Services Log\Microsoft-Workplace Join**의 이벤트 로그에서 상태 정보를 찾을 수도 있습니다.
  
**실패한 하이브리드 Azure AD 조인에 대한 가장 일반적인 원인은 다음과 같습니다.** 

- 컴퓨터가 조직의 내부 네트워크에 있지 않거나 온-프레미스 AD 도메인 컨트롤러에 연결되지 않은 VPN에 있습니다.

- 로컬 컴퓨터 계정으로 컴퓨터에 로그온됩니다. 

- 서비스 구성 문제: 

  - 페더레이션 서버가 **WIAORMULTIAUTHN**을 지원하도록 구성되었습니다. 

  - 컴퓨터가 속한 AD 포리스트의 Azure AD에서 확인된 도메인 이름을 가리키는 서비스 연결 지점 개체가 없습니다.

  - 장치 제한에 도달했습니다. 

## <a name="next-steps"></a>다음 단계

질문은 [장치 관리 FAQ](device-management-faq.md)를 참조하세요.  
