---
title: Datadog 문제 해결 - Azure 파트너 솔루션
description: 이 문서에서는 Azure의 Datadog 문제 해결에 대한 정보를 제공합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563711"
---
# <a name="troubleshooting-datadog-on-azure"></a>Azure의 Datadog 문제 해결

이 문서에는 Datadog를 사용하는 솔루션 문제 해결에 대한 정보가 포함되어 있습니다.

## <a name="purchase-errors"></a>구매 오류

* 유효한 신용 카드가 Azure 구독에 연결되어 있지 않거나 결제 방법이 구독과 연결되어 있지 않기 때문에 구매가 실패합니다.

  다른 Azure 구독을 사용합니다. 또는 구독에 대한 신용 카드나 결제 방법을 추가하거나 업데이트합니다. 자세한 내용은 [크레딧 및 결제 방법 업데이트](../../cost-management-billing/manage/change-credit-card.md)를 참조하세요.

* EA 구독은 Marketplace 구매를 허용하지 않습니다.

  다른 구독을 사용하세요. 또는 Marketplace 구매에 EA 구독을 사용할 수 있는지 확인합니다. 자세한 내용은 [Marketplace 구매 사용](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)을 참조하세요. 이러한 방법으로 문제가 해결되지 않으면 [Datadog 지원](https://www.datadoghq.com/support)에 문의하세요.

## <a name="unable-to-create-datadog-resource"></a>Datadog 리소스를 만들 수 없음

Azure Datadog 통합을 설정하려면 Azure 구독에 대한 **소유자** 액세스 권한이 있어야 합니다. 설치를 시작하기 전에 적절한 액세스 권한이 있는지 확인합니다.

## <a name="single-sign-on-errors"></a>Single Sign-On 오류

**Single Sign-On 설정을 저장할 수 없음** - 이 오류는 Datadog SAML 식별자를 사용 중인 다른 엔터프라이즈 앱이 있는 경우에 발생합니다. 사용 중인 앱을 찾으려면 기본 SAML 구성 섹션에서 **편집** 을 선택합니다.

이 문제를 해결하려면 다른 앱을 비활성화하거나 다른 앱을 Enterprise 앱으로 사용하여 Datadog에 SAML SSO를 설정합니다. 다른 앱을 사용하기로 결정한 경우 앱에 [필수 설정](create.md#configure-single-sign-on)이 있는지 확인합니다.

**Single Sign-On 설정 페이지에 앱이 표시되지 않음** - 먼저 애플리케이션 ID를 검색합니다. 결과가 표시되지 않는 경우 앱의 SAML 설정을 확인합니다. 그리드는 올바른 SAML 설정이 있는 앱만 표시합니다. 

식별자 URL은 `https://us3.datadoghq.com/account/saml/metadata.xml`이어야 합니다.

회신 URL은 `https://us3.datadoghq.com/account/saml/assertion`이어야 합니다.

다음 그림은 올바른 값을 보여 줍니다.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="AAD에서 Datadog 애플리케이션에 대한 SAML 설정을 확인합니다." border="true":::

**테넌트에 초대된 게스트 사용자가 Single Sign-On에 액세스할 수 없음** - 일부 사용자는 Azure Portal에 이메일 주소가 두 개 있습니다. 일반적으로 이메일 하나는 UPN(사용자 계정 이름)이고 다른 하나는 대체 이메일입니다.

게스트 사용자를 초대할 때 홈 테넌트 UPN을 사용합니다. UPN을 사용하여 Single Sign-On 프로세스 중에 이메일 주소를 동기화된 상태로 유지합니다. 사용자 Azure Portal의 오른쪽 위 모서리에서 이메일 주소를 검색하여 UPN을 찾을 수 있습니다.
  
## <a name="logs-not-being-emitted"></a>로그를 내보내지 않음

Azure Monitor 리소스 로그 범주에 나열된 리소스만 Datadog에 로그를 내보냅니다. 리소스가 Datadog에 로그를 내보내고 있는지 확인하려면 특정 리소스에 대한 Azure 진단 설정으로 이동합니다. Datadog 진단 설정이 있는지 확인합니다.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Azure 리소스에 대한 Datadog 진단 설정" border="true":::

## <a name="metrics-not-being-emitted"></a>메트릭을 내보내지 않음

Datadog 리소스에는 적절한 Azure 구독에서 **모니터링 읽기 권한자** 역할이 할당됩니다. 이 역할을 통해 Datadog 리소스는 메트릭을 수집하고 이러한 메트릭을 Datadog로 보낼 수 있습니다.

리소스에 올바른 역할 할당이 있는지 확인하려면 Azure Portal을 열고 구독을 선택합니다. 왼쪽 창에서 **IAM(Access Control)** 를 선택합니다. Datadog 리소스 이름을 검색합니다. 아래와 같이 Datadog 리소스에 **모니터링 읽기 권한자** 역할 할당이 있는지 확인합니다.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Azure 구독에서 Datadog 역할 할당" border="true":::

## <a name="datadog-agent-installation-fails"></a>Datadog 에이전트 설치 실패

Azure Datadog 통합은 가상 머신 또는 App Service에 Datadog 에이전트를 설치할 수 있는 기능을 제공합니다. Datadog 에이전트를 구성하는 경우 API 키 화면에서 **기본 키** 로 선택한 API 키가 사용됩니다. 기본 키를 선택하지 않으면 Datadog 에이전트 설치에 실패합니다.

Datadog 에이전트가 잘못된 키로 구성된 경우 API 키 화면으로 이동하여 **기본 키** 를 변경합니다. Datadog 에이전트를 제거한 후 다시 설치하여 새 API 키로 가상 머신을 구성해야 합니다.

## <a name="next-steps"></a>다음 단계

Datadog의 [인스턴스 관리](manage.md)에 대해 자세히 알아봅니다.
