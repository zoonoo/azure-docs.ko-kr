---
title: 유럽 고객에 대한 ID 데이터 스토리지 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 유럽 고객의 ID 관련 데이터를 저장하는 위치에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82a78953c4385f7688705d4ab3f697be9c3ddbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235158"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory에서 유럽 고객에 대한 ID 데이터 스토리지
Id 데이터는 Azure AD에서 Office 365 및 Azure 같은 Microsoft 온라인 서비스를 구독 하는 경우 조직에서 제공한 주소를 기반으로 하는 지리적 위치에 저장 됩니다. Id 데이터 저장 위치에 대 한 정보를 사용할 수 있습니다 합니다 [데이터가 어디에 있는?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft 보안 센터의 섹션입니다.

유럽에 있는 주소를 제공 하는 고객의 경우 Azure AD는 대부분의 유럽 데이터 센터 내에서 id 데이터를 유지 합니다. 이 문서에서는 Azure AD 서비스에서 유럽의 외부에 저장 되는 데이터에 대해 설명 합니다.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authentication (MFA)
    
- 전화 통화를 사용 하 여 모든 2 단계 인증 또는 SMS 미국 데이터 센터에서 시작 하 고 전역 공급자도 라우팅됩니다.
- 미국 데이터 센터에서 앱 시작 Microsoft Authenticator를 사용 하 여 알림을 푸시하십시오. 또한 장치 공급 업체 특정 서비스 play 및 유럽 외부 아마도 이러한 서비스에도 가져올 수 있습니다.
- OATH 코드는 미국에서 항상 유효성이 검사됩니다.

Azure Multi-factor Authentication (MFA 서버) 및 클라우드 기반 Azure MFA에서 수집 되는 사용자 정보에 대 한 자세한 내용은 참조 하세요. [Azure Multi-factor Authentication 사용자 데이터 컬렉션](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)합니다.

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 정책 구성 데이터 및 키 컨테이너는 미국 데이터 센터에 저장 됩니다. 이러한 사용자 개인 데이터가 없습니다. 정책 구성에 대한 자세한 내용은 [Azure Active Directory B2C: 기본 제공 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) 문서를 참조하세요.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
사용 하는 azure AD B2B 저장소 초대 연결 하 고 미국 데이터 센터에서 URL 정보를 리디렉션합니다. 또한 B2B 초대 수신을 구독 취소 하는 사용자의 전자 메일 주소 미국 데이터 센터에도 저장 됩니다.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS는 고객이 선택한 Azure Virtual Network와 동일한 위치에 사용자 데이터를 저장합니다. 따라서 네트워크가 유럽 밖에 있는 경우 데이터가 유럽 외부에 복제되고 저장됩니다.

## <a name="other-considerations"></a>기타 고려 사항

서비스 및 Azure AD와 통합 되는 응용 프로그램 id 데이터에 액세스할 수 있으며 각 서비스 및 해당 특정 서비스 및 응용 프로그램에서 id 데이터 처리 방법 및 회사 데이터에 대 한 저장소 요구 사항에 맞는지 여부를 결정 하는 데 사용할 응용 프로그램을 평가 합니다.

Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 [데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
위에서 설명한 특징 및 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Multi-Factor Authentication이란?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD 셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Azure Active Directory B2C란?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Azure AD B2B 공동 작업이란?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure AD(Active Directory) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
