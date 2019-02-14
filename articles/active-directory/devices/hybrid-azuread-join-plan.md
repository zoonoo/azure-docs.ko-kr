---
title: Azure AD(Azure Active Directory)에서 하이브리드 Azure Active Directory 조인 구현을 계획하는 방법 | Microsoft Docs
description: 하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2019
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: be66f24ec6532b93c4554568b0a58d467a09c600
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746424"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>방법: 하이브리드 Azure Active Directory 조인 구현 계획

사용자와 비슷한 방식으로 디바이스는 보호하려는 다른 ID가 되고, 언제 어디서나 리소스를 보호하는 데도 사용됩니다. 다음 방법 중 하나를 사용하여 디바이스의 ID를 Azure AD로 가져와서 이 목표를 달성할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. 이와 동시에 [조건부 액세스](../active-directory-conditional-access-azure-portal.md)를 사용하여 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

온-프레미스 Active Directory 환경을 사용하고, Azure AD에 도메인 가입 디바이스를 연결하려는 경우 하이브리드 Azure AD 가입 디바이스를 구성하여 이를 수행할 수 있습니다. 이 문서에서는 사용자 환경에서 하이브리드 Azure AD 조인을 구현하는 데 관련된 단계를 제공합니다. 


## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 [Azure Active Directory의 디바이스 관리 소개](../device-management-introduction.md)를 잘 알고 있다고 가정합니다.


## <a name="plan-your-implementation"></a>구현 계획

하이브리드 Azure AD 구현을 계획하려면 다음 사항을 숙지해야 합니다.

|   |   |
|---|---|
|![확인][1]|지원되는 디바이스 검토|
|![확인][1]|알아야 할 사항 검토|
|![확인][1]|디바이스의 하이브리드 Azure AD 조인을 제어하는 방법 검토|
|![확인][1]|시나리오 선택|


 

## <a name="review-supported-devices"></a>지원되는 디바이스 검토 

하이브리드 Azure AD 조인은 광범위한 Windows 디바이스를 지원합니다. 이전 버전의 Windows를 실행하는 디바이스에 대한 구성에는 추가 또는 다른 단계가 필요하므로 지원되는 디바이스는 두 가지 범주로 그룹화됩니다.

**Windows 현재 디바이스**

- 윈도우 10
    
- Windows Server 2016


Windows 데스크톱 운영 체제를 실행하는 디바이스의 경우 지원되는 버전은 Windows 10주년 업데이트(버전 1607) 이상입니다. 가장 좋은 방법은 최신 버전의 Windows 10으로 업그레이드하는 것입니다.



 **Windows 하위 수준 디바이스**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


첫 번째 계획 단계에서는 사용자 환경을 검토하고 Windows 하위 수준 디바이스를 지원해야 하는지 여부를 결정해야 합니다.



## <a name="review-things-you-should-know"></a>알아야 할 사항 검토

환경이 둘 이상의 Azure AD 테넌트에 ID 데이터를 동기화한 단일 포리스트로 구성된 경우 하이브리드 Azure AD 조인을 사용할 수 없습니다.

시스템 준비 도구(Sysprep)를 사용하는 경우 하이브리드 Azure AD 조인에 대해 구성되지 않은 Windows 설치에서 이미지를 만들어야 합니다.

VM(Virtual Machine) 스냅숏을 사용하여 추가 VM을 만드는 경우 하이브리드 Azure AD 조인에 대해 구성되지 않은 VM 스냅숏을 사용해야 합니다.

Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인:

- [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)을 통해 페더레이션되지 않은 환경에서 **지원됩니다**. 

- Seamless Single Sign On이 설정되지 않은 Azure AD 통과 인증을 사용할 경우 지원되지 **않습니다**.

- 자격 증명 로밍 또는 사용자 프로필 로밍을 사용하는 경우나 VDI(가상 데스크톱 인프라)를 사용하는 경우에 지원되지 **않습니다**.


DC(도메인 컨트롤러) 역할을 실행하는 Windows Server의 등록은 지원되지 않습니다.

조직에서 인증된 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우 Windows 10 컴퓨터에서 아웃바운드 프록시를 성공적으로 인증할 수 있는지 확인해야 합니다. Windows 10 컴퓨터는 머신 컨텍스트를 사용하여 디바이스 등록을 실행하므로 머신 컨텍스트를 사용하여 아웃바운드 프록시 인증을 구성해야 합니다.


하이브리드 Azure AD 조인은 Azure AD를 사용하여 온-프레미스 도메인 가입 디바이스를 자동으로 등록하는 프로세스입니다. 모든 디바이스가 자동으로 등록되지 않도록 하려는 경우가 있습니다. 이 경우 [디바이스의 하이브리드 Azure AD 조인을 제어하는 방법](hybrid-azuread-join-control.md)을 참조하세요.

Windows 10 도메인 조인 디바이스에서 이미 [Azure AD를 테넌트에 등록](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices)한 경우 먼저 해당 상태를 제거한 후에 하이브리드 Azure AD 조인을 사용하도록 설정할 것을 적극 권장합니다. Windows 10 1809 릴리스에서 이 이중 상태를 방지하기 위해 다음과 같이 변경되었습니다. 
 - 디바이스가 하이브리드 Azure AD에 조인되면 기존의 Azure AD 등록됨 상태가 자동으로 제거됩니다. 
 - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001 레지스트리 키를 추가하여 도메인 조인 디바이스가 Azure AD에 등록되지 않도록 방지할 수 있습니다.


## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>디바이스의 하이브리드 Azure AD 조인을 제어하는 방법 검토

하이브리드 Azure AD 조인은 Azure AD를 사용하여 온-프레미스 도메인 가입 디바이스를 자동으로 등록하는 프로세스입니다. 모든 디바이스가 자동으로 등록되지 않도록 하려는 경우가 있습니다. 예를 들어, 초기 롤아웃 동안 모든 항목이 예상대로 작동하는지 확인하려는 경우가 여기에 해당합니다.

자세한 내용은 [디바이스의 하이브리드 Azure AD 조인을 제어하는 방법](hybrid-azuread-join-control.md)을 참조하세요.

## <a name="select-your-scenario"></a>시나리오 선택

하이브리드 Azure AD 조인을 구성할 수 있는 시나리오는 다음과 같습니다.

- 관리되는 도메인
- 페더레이션된 도메인  



사용자 환경에 관리되는 도메인이 있는 경우 하이브리드 Azure AD 조인에서 지원하는 작업은 다음과 같습니다.

- PTA(통과 인증)

- PHS(암호 해시 동기화)

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [페더레이션된 도메인용 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)


- [관리되는 도메인용 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)


 필요한 버전의 Azure AD Connect를 설치할 수 없는 경우 [디바이스 등록을 수동으로 구성하는 방법](https://docs.microsoft.com/en-us/azure/active-directory/devices/hybrid-azuread-join-manual)을 참조하세요. 


## <a name="on-premises-ad-upn-support-in-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인에서 온-프레미스 AD UPN 지원

경우에 따라 온-프레미스 AD UPN이 Azure AD UPN과 다를 수 있습니다. 그러한 경우 Windows 10 하이브리드 Azure AD 조인은 [인증 방법](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), 도메인 유형 및 Windows 10 버전을 기반으로 온-프레미스 AD UPN에 대한 제한된 지원을 제공합니다. 사용자 환경에 있을 수 있는 온-프레미스 AD UPN에는 두 가지 유형이 있습니다.

 - 라우팅 가능 UPN: 라우팅 가능 UPN에는 도메인 등록 기관에 등록되어 유효한 것으로 확인된 도메인이 있습니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.org는 Contoso에서 소유하고 [Azure AD에서 확인](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)된 기본 도메인입니다.
 
 - 라우팅 불가능 UPN: 라우팅 불가능 UPN에는 확인된 도메인이 없습니다. 조직의 사설망 내에서만 적용됩니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.com은 온-프레미스 AD의 기본 도에미인이지만 인터넷에서 확인할 수 없는 도메인이며 Consoso의 네트워크 내에서만 사용됩니다.
 
아래 표에서는 Windows 10 하이브리드 Azure AD 조인에서 이러한 온-프레미스 AD UPN에 대한 지원을 자세히 설명합니다.

|온-프레미스 AD UPN 유형|도메인 유형|Windows 10 버전|설명|
|-----|-----|-----|-----|
|라우팅 가능|페더레이션 |1703 릴리스|일반 공급|
|라우팅 가능|관리|1709 릴리스|현재 비공개 미리 보기로 제공되고 있습니다. Azure AD SSPR이 지원되지 않음 |
|라우팅 불가능|페더레이션|1803 릴리스|일반 공급|
|라우팅 불가능|관리|지원되지 않음||



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [페더레이션된 도메인에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
> [관리되는 도메인에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
