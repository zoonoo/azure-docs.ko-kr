---
title: 유럽 고객에 대한 ID 데이터 스토리지 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 유럽 고객의 ID 관련 데이터를 저장하는 위치에 대해 알아봅니다.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 819f61f5391eef0c5f3d314d3b199dfe8e1a3560
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562037"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory에서 유럽 고객에 대한 ID 데이터 스토리지
Id 데이터는 Office 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공 하는 주소에 따라 지리적 위치에 Azure AD에 저장 됩니다. Id 데이터가 저장 되는 위치에 대 한 자세한 내용은 Microsoft 보안 센터의 [어디에 있는 데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

유럽에서 주소를 제공한 고객의 경우 Azure AD는 유럽 데이터 센터 내에서 대부분의 id 데이터를 유지 합니다. 이 문서에서는 Azure AD 서비스에 의해 유럽 외부에 저장 된 모든 데이터에 대 한 정보를 제공 합니다.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authentication (MFA)
    
- 전화 통화 또는 SMS를 사용 하는 모든 2 단계 인증은 미국 데이터 센터에서 시작 되며 전역 공급자에 의해 라우팅됩니다.
- Microsoft Authenticator 앱을 사용한 푸시 알림은 미국 데이터 센터에서 시작 됩니다. 또한 장치 공급 업체의 특정 서비스를 재생할 수 있으며 이러한 서비스는 유럽 외부에서 제공 될 수도 있습니다.
- OATH 코드는 미국에서 항상 유효성이 검사됩니다. 

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 정책 구성 데이터 및 키 컨테이너는 미국 데이터 센터에 저장 됩니다. 여기에는 사용자 개인 데이터가 포함 되지 않습니다. 정책 구성에 대한 자세한 내용은 [Azure Active Directory B2C: 기본 제공 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) 문서를 참조하세요.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B는 미국 데이터 센터에서 초대 및 리디렉션 URL 정보를 사용 하 여 초대를 저장 합니다. 또한 B2B 초대 수신을 취소 하는 사용자의 전자 메일 주소도 미국 데이터 센터에 저장 됩니다.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory 도메인 서비스 (Azure AD DS)

Azure AD DS는 고객이 선택한 Azure Virtual Network와 동일한 위치에 사용자 데이터를 저장합니다. 따라서 네트워크가 유럽 밖에 있는 경우 데이터가 유럽 외부에 복제되고 저장됩니다.

## <a name="other-considerations"></a>기타 고려 사항

Azure AD와 통합 되는 서비스 및 응용 프로그램은 id 데이터에 액세스할 수 있습니다. 특정 서비스 및 응용 프로그램에서 id 데이터를 처리 하는 방법 및 회사의 데이터 저장소 요구 사항을 충족 하는지 여부를 결정 하는 데 사용 하는 각 서비스 및 응용 프로그램을 평가 합니다.

Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 [데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
위에서 설명한 특징 및 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Multi-Factor Authentication이란?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD 셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Azure Active Directory B2C란?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Azure AD B2B 협업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure AD(Active Directory) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
