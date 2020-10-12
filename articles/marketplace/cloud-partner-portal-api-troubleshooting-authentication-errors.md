---
title: 일반적인 인증 오류 문제 해결 Azure Marketplace
description: Azure Marketplace에서 Cloud 파트너 포털 Api를 사용 하는 경우 일반적인 인증 오류에 대 한 지원을 제공 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 01af5357e4ae2f4dfb317a0931a8d0bc2b2d54e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89487320"
---
# <a name="troubleshooting-common-authentication-errors"></a>일반적인 인증 오류 해결

> [!NOTE]
> Cloud 파트너 포털 Api는와 통합 되며 파트너 센터에서 계속 작업 합니다. 전환에는 작은 변화가 도입 됩니다. [CLOUD 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md) 에 나열 된 변경 내용을 검토 하 여 파트너 센터로 전환 된 후 코드가 계속 작동 하는지 확인 합니다. CPP Api는 파트너 센터로 전환 하기 전에 이미 통합 된 기존 제품에만 사용 해야 합니다. 새 제품은 파트너 센터 제출 Api를 사용 해야 합니다.

이 문서에서는 Cloud 파트너 포털 API를 사용하는 경우 일반적인 인증 오류에 필요한 지원을 제공합니다.

## <a name="unauthorized-error"></a>권한 없음 오류 발생

`401 unauthorized` 오류가 일관되게 발생하면 유효한 액세스 토큰을 사용하는지 확인합니다.  [포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)에 설명된 기본 Azure AD(Azure Active Directory) 애플리케이션 및 서비스 주체를 아직 만들지 않았으면 만듭니다. 그런 다음, 애플리케이션 또는 간단한 HTTP POST 요청을 사용하여 액세스를 확인합니다.  사용자는 테 넌 트 ID, 응용 프로그램 ID, 개체 ID 및 비밀 키를 포함 하 여 액세스 토큰을 가져옵니다.

## <a name="forbidden-error"></a>사용할 수 없음 오류

`403 forbidden` 오류가 발생하면 Cloud 파트너 포털의 게시자 계정에 올바른 서비스 주체를 추가했는지 확인합니다. [필수 구성 요소](./cloud-partner-portal-api-prerequisites.md) 페이지의 단계에 따라 포털에 서비스 주체를 추가합니다.

올바른 서비스 주체를 추가한 경우 다른 모든 정보를 확인합니다. 포털에 입력한 개체 ID를 유의해서 확인합니다. Azure Active Directory 앱 등록 페이지에는 두 개의 개체 ID가 있으며 로컬 개체 ID를 사용해야 합니다. 앱의 **앱 등록** 페이지로 이동한 후 **로컬 디렉터리의 관리되는 애플리케이션** 아래의 앱 이름을 클릭하여 올바른 값을 확인할 수 있습니다. 이렇게 하면 앱의 로컬 속성으로 이동됩니다. 여기서 다음 그림과 같이 **속성** 페이지에서 올바른 개체 ID를 찾을 수 있습니다. 또한 서비스 주체를 추가하고 API를 호출할 때 올바른 게시자 ID를 사용하는지 확인합니다.
