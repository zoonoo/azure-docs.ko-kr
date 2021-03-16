---
title: Datadog 문제 해결-Azure 파트너 솔루션
description: 이 문서에서는 Azure의 Datadog 문제 해결에 대 한 정보를 제공 합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563711"
---
# <a name="troubleshooting-datadog-on-azure"></a>Azure의 Datadog 문제 해결

이 문서에는 Datadog를 사용 하는 솔루션 문제 해결에 대 한 정보가 포함 되어 있습니다.

## <a name="purchase-errors"></a>구매 오류

* 유효한 신용 카드가 Azure 구독에 연결 되어 있지 않거나 지불 방법이 구독과 연결 되어 있지 않기 때문에 구매가 실패 합니다.

  다른 Azure 구독을 사용 합니다. 또는 구독에 대 한 신용 카드나 지불 방법을 추가 하거나 업데이트 합니다. 자세한 내용은 [크레딧 및 지불 방법 업데이트](../../cost-management-billing/manage/change-credit-card.md)를 참조 하세요.

* EA 구독은 Marketplace 구매를 허용 하지 않습니다.

  다른 구독을 사용 합니다. 또는 EA 구독이 Marketplace 구매에 사용 되는지 확인 합니다. 자세한 내용은 [Marketplace 구매 사용](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)을 참조 하세요. 이러한 방법으로 문제가 해결 되지 않으면 [Datadog 지원](https://www.datadoghq.com/support)에 문의 하세요.

## <a name="unable-to-create-datadog-resource"></a>Datadog 리소스를 만들 수 없습니다.

Azure Datadog 통합을 설정 하려면 Azure 구독에 대 한 **소유자** 액세스 권한이 있어야 합니다. 설치를 시작 하기 전에 적절 한 액세스 권한이 있는지 확인 합니다.

## <a name="single-sign-on-errors"></a>Single sign-on 오류

**Single sign-on 설정을 저장할 수 없음** -이 오류는 Datadog SAML 식별자를 사용 하는 다른 엔터프라이즈 앱이 있는 경우에 발생 합니다. 사용 중인 앱을 찾으려면 기본 SAML 구성 섹션에서 **편집** 을 선택 합니다.

이 문제를 해결 하려면 다른 앱을 사용 하지 않도록 설정 하거나 다른 앱을 엔터프라이즈 앱으로 사용 하 여 Datadog로 SAML SSO를 설정 합니다. 다른 앱을 사용 하기로 결정 한 경우 앱에 [필요한 설정이](create.md#configure-single-sign-on)있는지 확인 합니다.

**Single sign-on 설정에서 앱이 표시 되지 않습니다. 페이지** -먼저 응용 프로그램 ID를 검색 합니다. 결과가 표시 되지 않는 경우 앱의 SAML 설정을 확인 합니다. 그리드는 올바른 SAML 설정이 있는 앱만 표시 합니다. 

식별자 URL은 이어야 합니다 `https://us3.datadoghq.com/account/saml/metadata.xml` .

회신 URL은 이어야 합니다 `https://us3.datadoghq.com/account/saml/assertion` .

다음 그림은 올바른 값을 보여 줍니다.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="AAD에서 Datadog 응용 프로그램에 대 한 SAML 설정을 확인 합니다." border="true":::

**테 넌 트에 초대 된 게스트 사용자가 Single sign-on에 액세스할 수 없습니다** . 일부 사용자는 Azure Portal에 전자 메일 주소가 두 개 있습니다. 일반적으로 한 메일은 UPN (사용자 계정 이름)이 고 다른 하나는 대체 전자 메일입니다.

게스트 사용자를 초대할 때 홈 테 넌 트 UPN을 사용 합니다. UPN을 사용 하 여 Single sign-on 프로세스 중에 전자 메일 주소를 동기화 된 상태로 유지 합니다. 사용자 Azure Portal의 오른쪽 위 모서리에서 전자 메일 주소를 검색 하 여 UPN을 찾을 수 있습니다.
  
## <a name="logs-not-being-emitted"></a>로그를 내보내지 않음

Azure Monitor 리소스 로그 범주에 나열 된 리소스만 로그를 Datadog에 내보냅니다. 리소스가 Datadog에 로그를 내보내고 있는지 확인 하려면 특정 리소스에 대 한 Azure 진단 설정으로 이동 합니다. Datadog 진단 설정이 있는지 확인 합니다.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Azure 리소스에 대 한 Datadog 진단 설정" border="true":::

## <a name="metrics-not-being-emitted"></a>메트릭을 내보내지 않음

Datadog 리소스에는 적절 한 Azure 구독에서 **모니터링 판독기** 역할이 할당 됩니다. 이 역할을 통해 Datadog 리소스는 메트릭을 수집 하 고 이러한 메트릭을 Datadog로 보낼 수 있습니다.

리소스에 올바른 역할 할당이 있는지 확인 하려면 Azure Portal을 열고 구독을 선택 합니다. 왼쪽 창에서 **Access Control (IAM)** 을 선택 합니다. Datadog 리소스 이름을 검색 합니다. 아래와 같이 Datadog 리소스에 **모니터링 판독기** 역할 할당이 있는지 확인 합니다.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Azure 구독에서 Datadog 역할 할당" border="true":::

## <a name="datadog-agent-installation-fails"></a>Datadog 에이전트 설치 실패

Azure Datadog 통합은 가상 머신 또는 app service에 Datadog 에이전트를 설치 하는 기능을 제공 합니다. Datadog 에이전트를 구성 하는 경우 API 키 화면에서 **기본 키** 로 선택한 api 키가 사용 됩니다. 기본 키를 선택 하지 않으면 Datadog 에이전트가 설치 되지 않습니다.

Datadog 에이전트가 잘못 된 키로 구성 된 경우 API 키 화면으로 이동 하 여 **기본 키** 를 변경 합니다. Datadog 에이전트를 제거한 후 다시 설치 하 여 새 API 키로 가상 머신을 구성 해야 합니다.

## <a name="next-steps"></a>다음 단계

Datadog의 [인스턴스를 관리 하](manage.md) 는 방법에 대해 알아봅니다.
