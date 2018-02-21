---
title: "Azure Active Directory 장치 기반 조건부 액세스 정책 구성 | Microsoft Docs"
description: "Azure Active Directory 장치 기반 조건부 액세스 정책을 구성하는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2354a8bf81189f70bb8d0d63c3df3236403c11fc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Azure Active Directory 장치 기반 조건부 액세스 정책 구성

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 리소스를 액세스하는 방법을 미세 조정할 수 있습니다. 예를 들어 특정 리소스에 대한 액세스를 신뢰할 수 있는 장치로 제한합니다. 신뢰할 수 있는 장치를 요구하는 조건부 액세스 정책을 장치 기반 조건부 액세스 정책이라고도 합니다.

이 항목에서는 Azure AD 연결 응용 프로그램에 대한 장치 기반 조건부 액세스 정책을 구성하는 방법에 대한 정보를 제공합니다. 


## <a name="before-you-begin"></a>시작하기 전에

장치 기반 조건부 액세스는 **Azure AD 조건부 액세스**와 **Azure AD 장치 관리**를 함께 연결합니다. 이러한 내용에 익숙하지 않은 경우 먼저 다음 항목을 읽어보세요.

- **[Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)** - 이 항목에서는 조건부 액세스 및 관련 용어에 대한 개념적 개요를 제공합니다.

- **[Azure Active Directory의 장치 관리 소개](device-management-introduction.md)**  - 이 항목에서는 Azure AD에 장치를 연결할 때 사용할 수 있는 다양한 옵션에 대해 간략히 설명합니다. 


## <a name="trusted-devices"></a>신뢰할 수 있는 장치

모바일 우선, 클라우드 우선 세계에서 Azure Active Directory는 어디에서나 장치, 앱 및 서비스에 대한 Single Sign-On을 가능하게 합니다. 작업 환경의 특정 리소스의 경우, 적절한 사용자에게 액세스 권한을 부여하는 것만으로 충분하지 않을 수 있습니다. 적절한 사용자 외에도, 리소스에 액세스할 때 신뢰할 수 있는 장치를 사용하도록 요구할 수 있습니다. 사용자 환경에서 다음 구성 요소 중에서 장치를 신뢰할 수 있는 장치로 만들어주는 요인을 정의할 수 있습니다.

- 장치의 [장치 플랫폼](active-directory-conditional-access-conditions.md#device-platforms)
- 장치는 규정을 준수하는지 여부
- 장치가 도메인에 가입되어 있는지 여부 

[장치 플랫폼](active-directory-conditional-access-conditions.md#device-platforms)은 다음 장치에서 실행되는 운영 체제를 특징으로 합니다. 장치 기반 조건부 액세스 정책에서 특정 리소스에 대 한 액세스를 특정 장치 플랫폼으로 제한할 수 있습니다.



장치 기반 조건부 액세스 정책에서 신뢰할 수 있는 장치를 규격으로 표시하도록 요구할 수 있습니다.

![클라우드 앱](./media/active-directory-conditional-access-policy-connected-applications/24.png)

장치는 다음을 통해 디렉터리에 규격으로 표시할 수 있습니다.

- Intune 
- Azure AD 통합을 통해 Windows 10 장치를 관리하는 타사 모바일 장치 관리 시스템 
 
  

Azure AD에 연결된 장치만 규격으로 표시할 수 있습니다. 장치를 Azure Active Directory에 연결하려는 경우 다음 옵션 중에서 선택할 수 있습니다. 

- Azure AD 등록
- Azure AD 가입
- 하이브리드 Azure AD 가입

    ![클라우드 앱](./media/active-directory-conditional-access-policy-connected-applications/26.png)

온-프레미스 AD(Active Directory) 공간을 설정한 경우 Azure AD에 연결되어 있지 않지만 AD에 가입된 장치는 신뢰할 수 있는 장치로 고려할 수 있습니다.

![클라우드 앱](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>다음 단계

환경에서 장치 기반 조건부 액세스 정책을 구성하기 전에 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 확인해야 합니다.

