---
title: 방법 - Azure Active Directory 조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 관리 장치 필요 | Microsoft Docs
description: 클라우드 앱 액세스에 대한 관리 장치가 필요한 Azure AD(Azure Active Directory) 장치 기반 조건부 액세스 정책을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 066d25e8953a2be4bd64cdd1af79b7f2a25dd5f9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035301"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>방법: 조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 관리 장치 필요

모바일 우선, 클라우드 우선 세계에서 Azure AD(Active Directory)는 어디에서나 앱 및 서비스에 대한 Single Sign-On을 가능하게 합니다. 권한 있는 사용자는 모바일 및 개인 장치를 포함하여 광범위한 장치에서 클라우드 앱에 액세스할 수 있습니다. 하지만 여러 환경에는 보안 및 준수를 위해 표준을 충족하는 장치에 의해서만 액세스되어야 하는 몇 가지 앱이 있습니다. 이러한 장치는 관리 장치라고도 합니다. 

이 아티클에서는 환경에서 특정 클라우드 앱에 액세스하기 위해 관리 장치가 필요한 조건부 액세스 정책을 구성할 수 있는 방법을 설명합니다. 


## <a name="prerequisites"></a>필수 조건

클라우드 앱 액세스에 대해 관리 장치가 필요하면 **Azure AD 조건부 액세스**와 **Azure AD 장치 관리**를 함께 연결합니다. 이러한 내용에 익숙하지 않은 경우 먼저 다음 항목을 읽어보세요.

- **[Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)** - 이 아티클에서는 조건부 액세스 및 관련 용어에 대한 개념적 개요를 제공합니다.

- **[Azure Active Directory의 장치 관리 소개](device-management-introduction.md)**  - 이 아티클에서는 장치를 조직에서 제어해야 하는 다양한 옵션에 대해 간략히 설명합니다. 


## <a name="scenario-description"></a>시나리오 설명

보안과 생산성 간의 균형을 유지하기가 어렵습니다. 클라우드 리소스에 액세스하도록 지원되는 장치의 범위가 넓어지면 사용자의 생산성을 개선할 수 있습니다. 반대로 환경에서 특정 리소스에 알려지지 않은 보호 수준을 가진 장치가 액세스하지 않도록 하는 것이 좋습니다. 영향을 받는 리소스의 경우 사용자가 관리 장치를 사용해서만 액세스할 수 있도록 해야 합니다. 

Azure AD 조건부 액세스를 사용하면 액세스 권한을 부여하는 단일 정책으로 이 요구 사항을 처리할 수 있습니다.

- 선택한 클라우드 앱으로

- 선택한 사용자 및 그룹에 대해

- 관리 장치 필요


## <a name="managed-devices"></a>관리되는 장치  

간단히 말해서 관리되는 장치는 *일종*의 조직 제어 하에 있는 장치입니다. Azure AD에서 관리되는 장치의 필수 조건은 Azure AD에 등록된 것입니다. 장치를 등록하면 장치 개체 형태로 장치의 ID가 만들어집니다. 이 개체는 Azure에서 장치에 대한 상태 정보를 추적하는 데 사용됩니다. Azure AD 관리자는 이미 이 개체를 사용하여 장치의 상태를 토글(설정/해제)할 수 있습니다.
  
![장치 기반 조건](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Azure AD에 등록된 장치를 가져오는 데는 세 가지 옵션이 있습니다.

- **[Azure AD 등록 장치](device-management-introduction.md#azure-ad-registered-devices)**  - Azure AD에 등록된 개인 장치를 가져옵니다.

- **[Azure AD 조인 장치](device-management-introduction.md#azure-ad-joined-devices)** - Azure AD에 등록된 온-프레미스 AD에 조인되지 않은 조직의 Windows 10 장치를 가져옵니다. 

- **[Azure AD 조인 장치](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  - Azure AD에 등록된 온-프레미스 AD에 조인된 Windows 10 장치를 가져옵니다.

관리되는 장치가 되려면, 등록된 장치는 **하이브리드 Azure AD 조인 장치** 또는 **준수 상태로 표시된 장치**여야 합니다.  

![장치 기반 조건](./media/active-directory-conditional-access-policy-connected-applications/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 조인된 장치 필요

조건부 액세스 정책에서 **하이브리드 Azure AD 조인 장치 필요**를 선택하면 관리되는 장치를 사용해서만 선택한 클라우드 앱에 액세스할 수 있도록 명시할 수 있습니다. 

![장치 기반 조건](./media/active-directory-conditional-access-policy-connected-applications/10.png)

이 설정은 온-프레미스 AD에 조인된 Windows 10 장치에만 적용됩니다. 등록된 Windows 10 장치를 가져오기 위한 [자동화된 프로세스](device-management-hybrid-azuread-joined-devices-setup.md)인 하이브리드 Azure AD 조인을 사용하여 이러한 장치를 Azure AD에만 등록할 수 있습니다. 

![장치 기반 조건](./media/active-directory-conditional-access-policy-connected-applications/45.png)

하이브리드 Azure AD 조인 장치를 관리되는 장치로 어떻게 만드나요?  온-프레미스 AD에 조인된 장치의 경우, 이러한 장치에 대한 제어는 장치를 관리하는 **SCCM(System Center Configuration Manager)** 또는 **GP(그룹 정책)** 과 같은 관리 솔루션을 사용하여 시행되는 것으로 가정합니다. Azure AD가 이러한 방법 중 어떤 것을 장치에 적용할지 결정할 방법이 없기 때문에 하이브리드 Azure AD 조인 장치를 요구하는 것은 관리되는 장치를 필요로 하는 비교적 약한 메커니즘입니다. 온-프레미스 도메인에 가입된 장치에 적용되는 방법이 관리되는 장치(이러한 장치가 하이브리드 Azure AD 조인 장치이기도 하다면)를 구성할 만큼 충분히 강력한지 여부를 판단하는 것은 관리자의 몫입니다.


## <a name="require-device-to-be-marked-as-compliant"></a>장치를 준수 상태로 표시해야 함

*장치를 준수 상태로 표시해야 하는* 옵션은 관리되는 장치를 요청하는 가장 강력한 형태입니다.

![장치 기반 조건](./media/active-directory-conditional-access-policy-connected-applications/11.png)

이 옵션을 사용하려면 장치를 Azure AD에 등록해야 하며 다음과 같이 준수 상태로도 표시되어야 합니다.
         
- Intune
- Azure AD 통합을 통해 Windows 10 장치를 관리하는 타사 MDM(모바일 장치 관리) 시스템입니다. Windows 10 이외의 장치 OS 형식에 대한 타사 MDM 시스템은 지원되지 않습니다.
 
![장치 기반 조건](./media/active-directory-conditional-access-policy-connected-applications/46.png)



준수 상태로 표시된 장치의 경우 다음과 같이 가정할 수 있습니다. 

- 직원이 회사 데이터에 액세스하는 데 사용하는 모바일 장치가 관리됩니다.
- 직원이 사용하는 모바일 앱이 관리됩니다.
- 직원이 정보에 액세스하고 공유하는 방식을 제어함으로써 회사 정보가 보호됩니다.
- 장치와 해당 앱은 회사 보안 요구 사항을 준수합니다.




## <a name="next-steps"></a>다음 단계

환경에서 장치 기반 조건부 액세스 정책을 구성하기 전에 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 확인해야 합니다.

