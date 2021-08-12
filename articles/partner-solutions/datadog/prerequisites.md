---
title: Azure의 Datadog에 대한 필수 구성 요소 - Azure 파트너 솔루션
description: 이 문서에서는 Azure 환경을 구성하여 Datadog 인스턴스를 만드는 방법을 설명합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 637d3cfb3baf8a53cb62943419077fad0f70d33d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110657089"
---
# <a name="configure-environment-before-datadog-deployment"></a>Datadog 배포 전에 환경 구성

이 문서에서는 Datadog의 첫 번째 인스턴스를 배포하기 전에 환경을 설정하는 방법을 설명합니다. 이러한 조건은 빠른 시작을 완료하기 위한 필수 구성 요소입니다.

## <a name="access-control"></a>Access Control

Azure Datadog 통합을 설정하려면 Azure 구독에 대한 **소유자** 액세스 권한이 있어야 합니다. 설정을 시작하기 전에 [적절한 액세스 권한이 있는지 확인](../../role-based-access-control/check-access.md)하세요.

## <a name="add-enterprise-application"></a>엔터프라이즈 애플리케이션 추가
 
Datadog 리소스 내에서 SAML(Security Assertion Markup Language) SSO(Single Sign-On) 기능을 사용하려면 엔터프라이즈 애플리케이션을 설정해야 합니다. 엔터프라이즈 애플리케이션을 추가하려면 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체 소유자 역할 중 하나가 필요합니다.

다음 단계에 따라 엔터프라이즈 애플리케이션을 설정합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. **Azure Active Directory** 를 선택합니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
1. **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 에서 *Datadog* 을 검색합니다. 검색 결과를 선택하고 **추가** 를 선택합니다.

   :::image type="content" source="media/prerequisites/datadog-azure-ad-app-gallery.png" alt-text="AAD 엔터프라이즈 갤러리의 Datadog 애플리케이션" border="true":::

1. 앱을 만든 후에는 측면 패널에서 속성으로 이동합니다. **사용자 할당 필요** 를 **아니요** 로 설정하고 **저장** 을 선택합니다.

   :::image type="content" source="media/prerequisites/user-assignment-required.png" alt-text="Datadog 애플리케이션의 속성 설정" border="true":::

1. 측면 패널에서 **Single Sign-On** 으로 이동합니다. 그리고 **SAML** 을 선택합니다.

   :::image type="content" source="media/prerequisites/saml-sso.png" alt-text="SAML 인증" border="true":::

1. **Single Sign-On 설정 저장** 메시지가 표시되면 **예** 를 선택합니다.

   :::image type="content" source="media/prerequisites/save-sso.png" alt-text="Datadog 앱에 대한 Single Sign-On 저장" border="true":::

1. 이제 Single Sign-On 설정이 완료되었습니다.

## <a name="next-steps"></a>다음 단계

Datadog의 인스턴스를 만들려면 [빠른 시작: Datadog 시작](create.md)을 참조하세요.
