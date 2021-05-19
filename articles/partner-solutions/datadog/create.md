---
title: Datadog 만들기 - Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal을 사용하여 Datadog 인스턴스를 만드는 방법을 설명합니다.
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/05/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 3d9c364c1367986f4ebe47cc00b4cb8b2434b90c
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109517463"
---
# <a name="quickstart-get-started-with-datadog"></a>빠른 시작: Datadog 시작

이 빠른 시작에서는 Datadog 인스턴스를 만듭니다. 새 Datadog 조직을 만들 수도 있고 기존 Datadog 조직에 연결할 수도 있습니다. Azure는 **US3** 의 기존 Datadog 조직에만 연결됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Datadog 통합을 설정하려면 Azure 구독에 대한 **소유자** 액세스 권한이 있어야 합니다. 설치를 시작하기 전에 적절한 액세스 권한이 있는지 확인합니다.

Datadog 리소스 내에서 SAML(Security Assertion Markup Language) SSO(Single Sign-On) 기능을 사용하려면 엔터프라이즈 애플리케이션을 설정해야 합니다. 엔터프라이즈 애플리케이션을 추가하려면 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체 소유자 역할 중 하나가 필요합니다.

다음 단계에 따라 엔터프라이즈 애플리케이션을 설정합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. **Azure Active Directory** 를 선택합니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
1. **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 에서 *Datadog* 을 검색합니다. 검색 결과를 선택하고 **추가** 를 선택합니다.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="AAD 엔터프라이즈 갤러리의 Datadog 애플리케이션" border="true":::

1. 앱을 만든 후에는 측면 패널에서 속성으로 이동합니다. **사용자 할당 필요** 를 **아니요** 로 설정하고 **저장** 을 선택합니다.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Datadog 애플리케이션의 속성 설정" border="true":::

1. 측면 패널에서 **Single Sign-On** 으로 이동합니다. 그리고 **SAML** 을 선택합니다.

   :::image type="content" source="media/create/saml-sso.png" alt-text="SAML 인증" border="true":::

1. **Single Sign-On 설정 저장** 메시지가 표시되면 **예** 를 선택합니다.

   :::image type="content" source="media/create/save-sso.png" alt-text="Datadog 앱에 대한 Single Sign-On 저장" border="true":::

1. 이제 Single Sign-On 설정이 완료되었습니다.

## <a name="find-offer"></a>제안 찾기

Azure Portal을 사용하여 Datadog을 찾습니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하고 로그인합니다.

1. 최근 세션에서 **Marketplace** 를 방문한 경우 사용 가능한 옵션에서 아이콘을 선택합니다. 그렇지 않으면 _Marketplace_ 를 검색합니다.

    :::image type="content" source="media/create/marketplace.png" alt-text="Marketplace 아이콘":::

1. Azure Marketplace에서 **Datadog** 을 검색합니다.

1. 계획 개요 화면에서 **설정 + 구독** 을 선택합니다.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Azure Marketplace의 Datadog 애플리케이션":::

## <a name="create-a-datadog-resource-in-azure"></a>Azure에서 Datadog 리소스 만들기

포털에 Datadog 리소스를 만드는 양식이 표시됩니다.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Datadog 리소스 만들기" border="true":::

다음 값을 제공합니다.

|속성 | Description
|:-----------|:-------- |
| Subscription | Datadog 리소스를 만드는 데 사용할 Azure 구독을 선택합니다. 소유자 액세스 권한이 필요합니다. |
| 리소스 그룹 | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다. [리소스 그룹](../../azure-resource-manager/management/overview.md#resource-groups)은 Azure 솔루션과 관련된 리소스를 보관하는 컨테이너입니다. |
| 리소스 이름 | Datadog 리소스의 이름을 지정합니다. 이 이름은 새 Datadog 조직을 만들 때 새 Datadog 조직의 이름이 됩니다. |
| 위치 | 미국 서부 2를 선택합니다. 현재 유일하게 지원되는 지역은 미국 서부 2입니다. |
| Datadog 조직 | 새 Datadog 조직을 만들려면 **새로 만들기** 를 선택합니다. 기존 Datadog 조직에 연결하려면 **기존 조직** 을 선택합니다. |
| 요금제 | 새 조직을 만드는 경우 사용 가능한 Datadog 계획 목록에서 선택합니다. |
| 청구 기간 | 매월 |

기존 Datadog 조직에 연결하는 경우 다음 섹션을 참조하세요. 그렇지 않으면 **다음: 메트릭 및 로그** 를 선택하고 다음 섹션을 건너뜁니다.

## <a name="link-to-existing-datadog-organization"></a>기존 Datadog 조직에 연결

Azure의 새 Datadog 리소스를 **US3** 의 기존 Datadog 조직에 연결할 수 있습니다.

데이터 조직으로 **기존 조직** 을 선택한 다음, **Datadog 조직에 연결** 을 선택합니다.

:::image type="content" source="media/create/link-to-existing.png" alt-text="기존 Datadog 조직에 연결합니다." border="true":::

이 링크는 Datadog 인증 창을 엽니다. Datadog에 로그인합니다.

기본적으로 Azure는 현재 Datadog 조직을 Datadog 리소스에 연결합니다. 다른 조직에 연결하려면 아래와 같이 인증 창에서 적절한 조직을 선택합니다.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="연결하려는 적절한 Datadog 조직 선택" border="true":::

## <a name="configure-metrics-and-logs"></a>메트릭 및 로그 구성

Azure 리소스 태그를 사용하여 Datadog으로 전송할 메트릭 및 로그를 구성합니다. 특정 리소스에 대한 메트릭 및 로그를 포함하거나 제외할 수 있습니다.

**메트릭** 전송에 대한 태그 규칙은 다음과 같습니다.

- 기본적으로 가상 머신, 가상 머신 확장 집합 및 앱 서비스 플랜을 제외한 모든 리소스에 대한 메트릭이 수집됩니다.
- *포함* 태그가 지정된 가상 머신, 가상 머신 확장 집합 및 App Service 플랜은 Datadog으로 메트릭을 전송합니다.
- *제외* 태그가 지정된 가상 머신, 가상 머신 확장 집합 및 앱 서비스 플랜은 Datadog으로 메트릭을 전송하지 않습니다.
- 포함 규칙과 제외 규칙이 충돌하는 경우 제외 규칙이 우선 적용됩니다.

**로그** 전송에 대한 태그 규칙은 다음과 같습니다.

- 기본적으로 모든 리소스에 대한 로그가 수집됩니다.
- *포함* 태그가 지정된 Azure 리소스는 Datadog으로 로그를 전송합니다.
- *제외* 태그가 지정된 Azure 리소스는 Datadog으로 로그를 전송하지 않습니다.
- 포함 규칙과 제외 규칙이 충돌하는 경우 제외 규칙이 우선 적용됩니다.

예를 들어 아래 스크린샷은 *Datadog = True* 라는 태그가 지정된 가상 머신, 가상 머신 확장 집합 및 앱 서비스 요금제만 Datadog으로 메트릭을 보내는 태그 규칙을 보여줍니다.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="로그 및 메트릭 구성" border="true":::

Azure에서 Datadog으로 내보낼 수 있는 두 가지 유형의 로그가 있습니다.

1. **구독 수준 로그** - [컨트롤 플레인](../../azure-resource-manager/management/control-plane-and-data-plane.md)의 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 서비스 상태 이벤트에 대한 업데이트도 포함됩니다. 활동 로그를 사용하여 누가, 무엇을, 언제 쓸 것인지 쓰기 작업(PUT, POST, DELETE)에 대한 내용을 결정합니다. Azure 구독마다 활동 로그가 하나씩 있습니다.

1. **Azure 리소스 로그** - [데이터 평면](../../azure-resource-manager/management/control-plane-and-data-plane.md)의 Azure 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 예를 들어 Key Vault에서 비밀을 가져오는 것은 데이터 평면 작업입니다. 또는 데이터베이스에 대한 요청을 수행하는 것도 데이터 평면 작업입니다. 이러한 로그의 내용은 Azure 서비스와 리소스 종류에 따라 달라집니다.

Datadog에 구독 수준 로그를 보내려면 **구독 활동 로그 보내기** 를 선택합니다. 이 옵션을 선택하지 않은 상태로 두면 구독 수준 로그가 Datadog으로 전송되지 않습니다.

Datadog에 Azure 리소스 로그를 보내려면 **정의된 모든 리소스에 대한 Azure 리소스 로그 보내기** 를 선택합니다. Azure 리소스 로그의 유형은 [Azure Monitor 리소스 로그 범주](../../azure-monitor/essentials/resource-logs-categories.md)에 나열되어 있습니다.  Datadog으로 로그를 보내는 Azure 리소스 세트를 필터링하려면 Azure 리소스 태그를 사용합니다.

Azure는 Datadog으로 전송된 로그에 대한 요금을 청구합니다. 자세한 내용은 Azure Marketplace 파트너에게 전송된 [플랫폼 로그의 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

메트릭 및 로그 구성을 완료한 후에는 **다음: Single Sign-On** 을 선택합니다.

## <a name="configure-single-sign-on"></a>Single Sign-On 구성

조직에서 Azure Active Directory를 ID 공급자로 사용하는 경우 Azure Portal에서 Datadog으로 Single Sign-On을 설정할 수 있습니다. 조직에서 다른 ID 공급자를 사용하거나 지금은 Single Sign-On을 설정하지 않으려는 경우에는 이 섹션을 건너뛰어도 됩니다.

Datadog 리소스를 기존 Datadog 조직에 연결하는 경우 이 단계에서 Single Sign-On을 설정할 수 없습니다. 그 대신 Datadog 리소스를 만든 후 Single Sign-On을 설정합니다. 자세한 내용은 [Single Sign-On 다시 구성](manage.md#reconfigure-single-sign-on)을 참조하세요.

:::image type="content" source="media/create/linking-sso.png" alt-text="기존 Datadog 조직에 연결하는 Single Sign-On" border="true":::

Azure Active Directory를 통해 Single Sign-On을 설정하려면 **Azure Active Directory를 통해 Single Sign-On 사용** 의 확인란을 선택합니다.

Azure Portal이 Azure Active Directory에서 적절한 Datadog 애플리케이션을 검색합니다. 이 앱은 이전 단계에서 제공한 엔터프라이즈 앱과 일치합니다.

Datadog 앱 이름을 선택합니다.

:::image type="content" source="media/create/sso.png" alt-text="Datadog에 Single Sign-On을 사용하도록 설정합니다." border="true":::

완료되면 **다음: 태그** 를 선택합니다.

## <a name="add-custom-tags"></a>사용자 지정 태그 추가

새 Datadog 리소스에 대한 사용자 지정 태그를 지정할 수 있습니다. Datadog 리소스에 적용할 태그의 이름/값 쌍을 제공합니다.

:::image type="content" source="media/create/tags.png" alt-text="Datadog 리소스에 대한 사용자 지정 태그를 추가합니다." border="true":::

태그를 추가한 후에는 **다음: 검토+만들기** 를 선택합니다.

## <a name="review--create-datadog-resource"></a>Datadog 리소스 검토 + 만들기

선택한 항목과 사용 약관을 검토합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

:::image type="content" source="media/create/review-create.png" alt-text="Datadog 리소스를 검토하고 만듭니다." border="true":::

Azure가 Datadog 리소스를 배포합니다.

프로세스가 완료되면 **리소스로 이동** 을 선택하여 Datadog 리소스를 살펴봅니다.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Datadog 리소스 배포" border="true":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Datadog 리소스 관리](manage.md)
