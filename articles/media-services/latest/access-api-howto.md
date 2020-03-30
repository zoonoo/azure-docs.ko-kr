---
title: Azure AD 인증 시작
description: Azure 미디어 서비스 API를 사용 하려면 Azure Active Directory (Azure AD) 인증에 액세스 하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478254"
---
# <a name="get-credentials-to-access-media-services-api"></a>미디어 서비스 API에 액세스하는 자격 증명 받기  

Azure AD 인증을 사용하여 Azure 미디어 서비스 API에 액세스하는 경우 두 가지 인증 옵션이 있습니다.

- **서비스 주체** 인증(권장)

    서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 또는 마이크로 서비스)을 실행하는 앱입니다.
- **사용자 인증**

    Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 

이 문서에서는 미디어 서비스 API에 액세스하기 위한 자격 증명을 가져오는 단계를 설명합니다. 다음 탭에서 선택합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정. 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](create-account-howto.md)를 참조하세요.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

### <a name="api-access"></a>API 액세스 

**API 액세스** 페이지를 사용하면 API에 연결하는 데 사용할 인증 방법을 선택할 수 있습니다. 또한 이 페이지에서는 API에 연결하는 데 필요한 값도 제공합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Media Services 계정을 선택합니다.
2. Media Services API에 연결하는 방법을 선택합니다.
3. **미디어 서비스 API에 연결에서**연결하려는 미디어 서비스 API 버전을 선택합니다(V3는 서비스의 최신 버전입니다).

### <a name="service-principal-authentication--recommended"></a>서비스 주체 인증(권장)

Azure Active Directory(Azure AD) 앱 및 비밀을 사용하여 서비스를 인증합니다. 미디어 서비스 API를 호출하는 모든 중간 계층 서비스에 권장됩니다. 웹 앱, 함수, 논리 앱, API 및 마이크로 서비스가 있습니다. 권장되는 인증 방법입니다.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Azure AD 앱 및 비밀 관리

**AAD 앱 및 비밀 관리** 섹션에서 새 Azure AD 앱을 선택하거나 만들고 비밀을 생성할 수 있습니다. 보안을 위해 블레이드를 닫은 후에는 비밀을 표시할 수 없습니다. 응용 프로그램은 응용 프로그램 ID와 비밀을 인증에 사용하여 미디어 서비스에 대한 유효한 토큰을 얻습니다.

Azure AD 테넌트에 응용 프로그램을 등록하고 Azure 구독의 역할에 응용 프로그램을 할당할 수 있는 충분한 권한이 있는지 확인합니다. 자세한 내용은 [필수 사용 권한](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)을 참조하십시오.

#### <a name="connect-to-media-services-api"></a>미디어 서비스 API에 연결

**미디어 서비스 연결 API는** 서비스 주 체 응용 프로그램을 연결하는 데 사용하는 값을 제공합니다. 텍스트 값을 얻거나 JSON 또는 XML 블록을 복사할 수 있습니다.

### <a name="user-authentication"></a>사용자 인증

이 옵션은 앱을 사용하여 미디어 서비스 리소스와 상호 작용하는 Azure Active Directory의 직원 또는 구성원을 인증하는 데 사용할 수 있습니다. 대화형 애플리케이션은 먼저 사용자에게 사용자의 자격 증명을 묻는 메시지를 표시합니다. 이 인증 방법은 관리 응용 프로그램에만 사용해야 합니다.

#### <a name="connect-to-media-services-api"></a>미디어 서비스 API에 연결

자격 증명을 복사하여 사용자 응용 프로그램을 미디어 서비스 API 연결 섹션에서 **연결합니다.** 텍스트 값을 얻거나 JSON 또는 XML 블록을 복사할 수 있습니다.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>다음 단계

[자습서: 미디어 서비스 v3를 통해 비디오를 업로드, 인코딩 및 스트리밍합니다.](stream-files-tutorial-with-api.md)
