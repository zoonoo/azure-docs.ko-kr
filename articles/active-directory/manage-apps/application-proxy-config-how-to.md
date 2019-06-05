---
title: 애플리케이션 프록시 애플리케이션을 구성하는 방법 | Microsoft Docs
description: 만들기 및 몇 가지 간단한 단계에서 응용 프로그램 프록시 응용 프로그램을 구성 하는 방법을 알아봅니다
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9287aac567c8989564094564b92b82662e603f
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825928"
---
# <a name="how-to-configure-an-application-proxy-application"></a>애플리케이션 프록시 애플리케이션을 구성하는 방법

이 문서를 사용 하면 클라우드로 온-프레미스 응용 프로그램을 노출 하도록 Azure AD 내에서 응용 프로그램 프록시 응용 프로그램을 구성 하는 방법을 이해 하도록 도와줍니다.

## <a name="recommended-documents"></a>권장 문서 

관리 포털을 통한 애플리케이션 프록시 애플리케이션의 초기 구성 및 생성에 대해 배우려면 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)를 따릅니다.

커넥터를 구성하는 방법에 대한 자세한 내용은 [Azure Portal에서 애플리케이션 프록시 사용](application-proxy-add-on-premises-application.md)을 참조하세요.

인증서 업로드 및 사용자 지정 도메인 사용에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)을 참조하세요.

## <a name="create-the-applicationsetting-the-urls"></a>애플리케이션 만들기/URL 설정

[Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md) 문서의 단계에 따라 애플리케이션을 만드는 중 오류가 발생한 경우 오류 세부 정보에서 애플리케이션 문제 해결 방법에 대한 정보 및 제안 사항을 참조하세요. 대부분의 오류 메시지에는 제안 수정이 포함되어 있습니다. 일반적인 오류를 방지하려면 다음을 확인합니다.

-   애플리케이션 프록시 애플리케이션을 만들 수 있는 권한을 가진 관리자여야 합니다.

-   내부 URL이 고유해야 합니다.

-   외부 URL이 고유해야 합니다.

-   URL이 http 또는 https로 시작하고 "/"로 끝나야 합니다.

-   URL은 IP 주소가 아닌 도메인 이름이어야 합니다.

오류 메시지는 응용 프로그램을 만들 때 오른쪽 위 모서리에 표시 됩니다. 알림 아이콘을 선택하여 오류 메시지를 볼 수도 있습니다.

   ![알림 프롬프트](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>커넥터/커넥터 그룹 구성

커넥터 및 커넥터 그룹에 대한 경고로 인해 애플리케이션을 구성하는 데 어려움이 있는 경우 애플리케이션 프록시 사용에 대한 지침에서 커넥터 다운로드 방법에 대한 자세한 내용을 참조하세요. 커넥터에 대한 자세한 내용은 [커넥터 설명서](application-proxy-connectors.md)를 참조하세요.

커넥터가 비활성 상태이면 서비스에 연결할 수 없음을 의미합니다. 필요한 모든 포트가 열려 있지 않은 경우 이러한 오류가 종종 발생합니다. 필요한 포트 목록을 보려면 애플리케이션 프록시 사용 설명서의 필수 조건 섹션을 참조하세요.

## <a name="upload-certificates-for-custom-domains"></a>사용자 지정 도메인에 대한 인증서 업로드

사용자 지정 도메인을 사용하면 외부 URL의 도메인을 지정할 수 있습니다. 사용자 지정 도메인을 사용하려면 해당 도메인의 인증서를 업로드해야 합니다. 사용자 지정 도메인 및 인증서 사용에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)을 참조하세요. 

인증서를 업로드하는 데 문제가 발생하는 경우 포털의 오류 메시지에서 인증서 문제에 대한 추가 정보를 참조하세요. 인증서의 일반적인 문제는 다음과 같습니다.

-   만료된 인증서

-   자체 서명된 인증서

-   프라이빗 키가 없는 인증서

인증서를 업로드 하려고 하면 오류 메시지가 오른쪽 위 모서리에 표시 됩니다. 알림 아이콘을 선택하여 오류 메시지를 볼 수도 있습니다.

   ![알림 프롬프트](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>다음 단계
[Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)
