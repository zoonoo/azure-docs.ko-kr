---
title: Azure Digital Twins에서 다중 테넌트 응용 프로그램을 사용하는 방법 | Microsoft Docs
description: Azure Digital Twins에서 고객의 Azure Active Directory 테넌트를 등록하는 방법 이해
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324016"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Azure Digital Twins에서 다중 테넌트 응용 프로그램을 사용하는 방법

Azure Digital Twins 플랫폼을 사용하는 개발자는 다중 테넌트 응용 프로그램을 빌드할 가능성이 높습니다. *다중 테넌트 응용 프로그램*은 고유하고 독립적인 데이터 및 권한을 사용하여 여러 고객을 지원하는 데 사용되는 단일 프로비전 인스턴스입니다.

이 문서에서는 여러 Azure AD(Active Directory) 테넌트 및 고객을 지원하는 Azure Digital Twins 다중 테넌트 앱을 만드는 방법을 자세히 설명합니다.

## <a name="scenario-summary"></a>시나리오 요약

이 시나리오에서는 개발자 D 및 고객 C를 고려합니다.

- 개발자 D에게는 Azure AD 테넌트를 사용하는 Azure 구독이 있습니다.
- 개발자 D는 Azure 구독에 Digital Twins 인스턴스를 배포했습니다.
- Azure AD에서 개발자 D의 Azure AD 테넌트에서 서비스 사용자를 생성했으므로 개발자 D의 Azure AD 테넌트 내에서 사용자는 Digital Twins 서비스에 대한 토큰을 가져올 수 있습니다.
- 개발자 D는 이제 Digital Twins 관리 API와 직접 통합되는 모바일 앱을 만듭니다.
- 개발자 D는 고객 C가 모바일 응용 프로그램을 사용할 수 있도록 합니다.
- 이제 고객 C는 개발자 D의 응용 프로그램 내에서 Digital Twins 관리 API를 사용할 수 있도록 권한이 부여되어야 합니다.

  > [!IMPORTANT]
  > - 고객 C가 개발자 D의 응용 프로그램에 로그인하면 앱은 고객 C의 사용자에 대한 토큰을 취득하여 관리 API에 지시할 수 없습니다.
  > - Azure AD는 고객 C의 디렉터리 내에서 Digital Twins가 인식되지 않음을 나타내는 오류를 throw합니다.

## <a name="solution"></a>해결 방법

위의 시나리오를 해결하려면 고객 C의 Azure AD 테넌트 내에서 Digital Twins 서비스 사용자를 만드는 데 다음 작업이 필요합니다.

- 고객 C에게 Azure AD 테넌트를 사용하는 Azure 구독이 아직 없는 경우:

  - 고객 C의 Azure AD 테넌트 관리자는 [종량제 Azure 구독](https://azure.microsoft.com/offers/ms-azr-0003p/)을 취득해야 합니다.
  - 그런 다음, 고객 C의 Azure AD 테넌트 관리자는 [새 구독과 테넌트를 연결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)해야 합니다.

- [Azure Portal](https://portal.azure.com)에서 고객 C의 Azure AD 테넌트 관리자는 다음을 수행해야 합니다.
  1. **구독**을 엽니다.
  1. 개발자 D의 응용 프로그램에서 사용할 수 있는 Azure AD 테넌트를 포함한 구독을 선택합니다.
  1. **리소스 공급자**를 선택합니다.
  1. **Microsoft.IoTSpaces**를 검색합니다.
  1. **등록**을 클릭합니다.
  
## <a name="next-steps"></a>다음 단계

Azure Digital Twins에서 사용자 정의 함수를 사용하는 방법을 알아보려면 [Azure Digital Twins UDF](how-to-user-defined-functions.md)를 참고하세요.

역할 기반 액세스 제어를 사용하여 역할 할당에서 응용 프로그램을 추가로 보호하는 방법을 알아보려면 [Digital Twins 역할 기반 액세스 제어](security-create-manage-role-assignments.md)를 참고하세요.
