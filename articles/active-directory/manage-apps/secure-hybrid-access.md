---
title: Azure AD 보안 하이브리드 액세스 | Microsoft Docs
description: 이 문서에서는 레거시 온-프레미스, 퍼블릭 클라우드 또는 프라이빗 클라우드 애플리케이션을 Azure AD와 통합하기 위한 파트너 솔루션에 대해 설명합니다. 앱 배달 컨트롤러 또는 네트워크를 Azure AD에 연결하여 레거시 앱을 보호하세요.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 12/18/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295891afbb0136e0b05bcd49f4045e0e8bcff6e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763043"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>보안 하이브리드 액세스: 앱 제공 컨트롤러 및 네트워크를 사용하여 레거시 앱 보호

이제 기존 애플리케이션 배달 컨트롤러나 네트워크를 사용하여 온-프레미스 및 클라우드 레거시 인증 애플리케이션을 Azure AD에 연결하여 보호할 수 있습니다. 이러한 방식으로 Azure AD 조건부 액세스 및 Azure AD ID 보호 같은 Azure AD 기능을 사용하여 모든 애플리케이션에서 간극을 연결하고, 보안 테세를 강화할 수 있습니다.

기존 네트워킹 및 배달 컨트롤러를 사용하여 비즈니스 프로세스에 여전히 중요 하지만 이전에는 Azure AD로 보호할 수 없었는 레거시 애플리케이션을 쉽게 보호할 수 있습니다. 이러한 애플리케이션을 보호하는 데 필요한 모든 조건은 이미 충족되었을 것입니다.

![보안 하이브리드 액세스를 보여 주는 이미지](media/secure-hybrid-access/secure-hybrid-access.png)

다음 공급업체는 Azure AD와 통합하기 위한 미리 빌드한 솔루션과 자세한 지침을 제공합니다.

* [Akamai EAA(엔터프라이즈 애플리케이션 액세스)](../saas-apps/akamai-tutorial.md)
* [Citrix ADC(Application Delivery Controller)](../saas-apps/citrix-netscaler-tutorial.md)
* [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)
* [ZPA(Zscaler Private Access)](https://aka.ms/zscaler-hybridaccessguide)
