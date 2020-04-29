---
title: Azure Portal을 사용하여 Azure AD 인증 시작 | Microsoft Docs
description: Azure Portal을 사용하여 Azure AD(Azure Active Directory) 인증에 액세스하고 Azure Media Services API를 사용하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78330671"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure AD 인증 시작

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>최신 버전인 [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)을 확인 하세요. 또한 [v2에서 v3로 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 을 참조 하세요.

Azure Portal을 사용하여 Azure AD(Azure Active Directory) 인증에 액세스하고 Azure Media Services API에 액세스하는 방법을 알아봅니다.

## <a name="prerequisites"></a>전제 조건

- Azure 계정. 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조하세요.

Azure Media Services와 함께 Azure AD 인증을 사용할 때 두 가지 인증 옵션이 제공됩니다.

- **서비스 사용자 인증**. 서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 또는 마이크로 서비스)을 실행하는 앱입니다.
- **사용자 인증**. Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 

## <a name="access-the-media-services-api"></a>Media Services API 액세스

이 페이지에서 API에 연결 하는 데 사용 하려는 인증 방법을 선택할 수 있습니다. 또한이 페이지는 API에 연결 하는 데 필요한 값을 제공 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Media Services 계정을 선택합니다.
2. Media Services API에 연결하는 방법을 선택합니다.
3. **MEDIA SERVICES api에 연결**에서 연결 하려는 Media Services api 버전을 선택 합니다.

## <a name="service-principal-authentication--recommended"></a>서비스 주체 인증 (권장)

Azure Active Directory (Azure AD) 앱 및 암호를 사용 하 여 서비스를 인증 합니다. Media Services API를 호출 하는 중간 계층 서비스에 권장 됩니다. Web Apps, 함수, Logic Apps, Api 및 마이크로 서비스를 예로 들 수 있습니다. 권장 되는 인증 방법입니다.

### <a name="manage-your-azure-ad-app-and-secret"></a>Azure AD 앱 및 비밀 관리

**AAD 앱 및 비밀 관리** 섹션에서 새 Azure AD 앱을 선택 하거나 만들고 비밀을 생성할 수 있습니다. 보안을 위해 블레이드를 닫은 후에는 비밀을 표시할 수 없습니다. 응용 프로그램은 인증에 응용 프로그램 ID 및 암호를 사용 하 여 media services에 대 한 유효한 토큰을 가져옵니다.

Azure AD 테 넌 트에 응용 프로그램을 등록 하 고 Azure 구독의 역할에 응용 프로그램을 할당할 수 있는 충분 한 권한이 있는지 확인 합니다. 자세한 내용은 [필요한 권한](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)을 참조 하세요.

### <a name="connect-to-media-services-api"></a>Media Services API에 연결

**Media Services에 연결 API** 는 서비스 사용자 응용 프로그램에 연결 하는 데 사용 하는 값을 제공 합니다. 텍스트 값을 가져오거나 JSON 또는 XML 블록을 복사할 수 있습니다.

## <a name="user-authentication"></a>사용자 인증

이 옵션은 앱을 사용 하 여 Media Services 리소스와 상호 작용 하는 Azure Active Directory의 직원 또는 멤버를 인증 하는 데 사용할 수 있습니다. 대화형 애플리케이션은 먼저 사용자에게 사용자의 자격 증명을 묻는 메시지를 표시합니다. 이 인증 방법은 관리 응용 프로그램에만 사용 해야 합니다.

### <a name="connect-to-media-services-api"></a>Media Services API에 연결

**MEDIA SERVICES API에 연결** 섹션에서 사용자 응용 프로그램을 연결 하려면 자격 증명을 복사 합니다. 텍스트 값을 가져오거나 JSON 또는 XML 블록을 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-portal-upload-files.md) 시작
