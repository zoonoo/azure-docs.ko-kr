---
title: 유럽 고객을 위한 ID 데이터 저장소 - Azure AD
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
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422996"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory에서 유럽 고객에 대한 ID 데이터 스토리지
ID 데이터는 Office 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공한 주소를 기반으로 지리적 위치에 Azure AD에 의해 저장됩니다. ID 데이터가 저장되는 위치에 대한 자세한 내용은 Microsoft 신뢰 센터의 [데이터 위치](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

유럽에서 주소를 제공한 고객의 경우 Azure AD는 대부분의 ID 데이터를 유럽 데이터 센터 내에 유지합니다. 이 문서는 Azure AD 서비스에 의해 유럽 외부에 저장되는 모든 데이터에 대한 정보를 제공합니다.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>마이크로소프트 Azure 다단계 인증(MFA)
    
- 전화 통화 또는 SMS를 사용하는 모든 이중 인증은 미국 데이터 센터에서 시작되었으며 글로벌 공급자에 의해 라우팅됩니다.
- Microsoft 인증자 앱을 사용하는 푸시 알림은 미국 데이터 센터에서 시작됩니다. 또한 장치 공급업체의 특정 서비스도 작동할 수 있으며 이러한 서비스는 유럽 외부에서 도서 될 수 있습니다.
- OATH 코드는 미국에서 항상 유효성이 검사됩니다. 

Azure 다단계 인증 서버(MFA Server) 및 클라우드 기반 Azure MFA에서 수집하는 사용자 정보에 대한 자세한 내용은 [Azure 다단계 인증 사용자 데이터 컬렉션을](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)참조하십시오.

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure AD B2C(Azure Active Directory B2C)

Azure AD B2C 정책 구성 데이터 및 키 컨테이너는 미국 데이터 센터에 저장됩니다. 여기에는 사용자 개인 데이터가 포함되지 않습니다. 정책 구성에 대한 자세한 내용은 [Azure Active Directory B2C: 기본 제공 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) 아티클을 참조하세요.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure AD B2B(Azure Active Directory B2B) 
    
Azure AD B2B는 미국 데이터 센터에 참조 링크가 있는 초대장을 저장하고 URL 정보를 리디렉션합니다. 또한 B2B 초대를 수신하지 않는 사용자의 이메일 주소도 미국 데이터 센터에 저장됩니다.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure AD DS(Azure Active Directory Domain Services)

Azure AD DS는 고객이 선택한 Azure Virtual Network와 동일한 위치에 사용자 데이터를 저장합니다. 따라서 네트워크가 유럽 밖에 있는 경우 데이터가 유럽 외부에 복제되고 저장됩니다.

## <a name="federation-in-microsoft-exchange-server-2013"></a>마이크로소프트 교환 서버에서 연맹 2013
    
- 응용 프로그램 식별자(AppID) - Exchange 조직을 식별하기 위해 Azure Active Directory 인증 시스템에서 생성된 고유 번호입니다.
- 응용 프로그램에 대한 승인된 페더레이션 도메인 목록
- 응용 프로그램의 토큰 서명 공개 키 

Microsoft Exchange 서버의 페더레이션에 대한 자세한 내용은 [페더레이션: Exchange 2013 도움말](https://docs.microsoft.com/exchange/federation-exchange-2013-help) 문서를 참조하십시오.


## <a name="other-considerations"></a>기타 고려 사항

Azure AD와 통합되는 서비스 및 응용 프로그램은 ID 데이터에 액세스할 수 있습니다. 사용하는 각 서비스 및 응용 프로그램을 평가하여 특정 서비스 및 응용 프로그램에서 ID 데이터가 처리되는 방식과 해당 서비스 저장소 요구 사항을 충족하는지 여부를 결정합니다.

Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 [데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
위에서 설명한 특징 및 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Multi-Factor Authentication이란?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD 셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Azure Active Directory B2C란?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Azure AD B2B 협업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure AD(Active Directory) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
