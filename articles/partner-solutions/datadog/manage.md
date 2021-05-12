---
title: Datadog 리소스 관리 - Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal의 Datadog 리소스 관리에 대해 설명합니다. Single Sign-On을 설정하고, Confluent 조직을 삭제하고, 지원을 받는 방법을 알아봅니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046354"
---
# <a name="manage-the-datadog-resource"></a>Datadog 리소스 관리

이 문서에서는 Datadog와의 Azure 통합에 대한 설정을 관리하는 방법을 보여 줍니다.

## <a name="resource-overview"></a>리소스 개요

Datadog 리소스에 대한 세부 정보를 보려면 왼쪽 창에서 **개요** 를 선택합니다.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Datadog 리소스 개요" border="true" lightbox="media/manage/resource-overview.png":::

세부 정보는 다음과 같습니다.

- 리소스 그룹 이름
- 위치/영역
- Subscription
- 태그들
- Datadog 조직에 대한 Single Sign-On 링크
- Datadog 제품/플랜
- 청구 기간

또한 Datadog 대시보드, 로그, 호스트 맵에 대한 링크도 제공합니다.

개요 화면에서는 Datadog에 로그 및 메트릭을 보내는 리소스에 대한 요약 정보를 제공합니다.

- 리소스 종류 – Azure 리소스 종류입니다.
- 총 리소스 수 - 리소스 종류에 대한 모든 리소스의 수입니다.
- 리소스 전송 로그 수 – 통합을 통해 Datadog로 로그를 전송하는 리소스의 수입니다.
- 리소스 전송 메트릭 – 통합을 통해 Datadog로 메트릭을 전송하는 리소스의 수입니다.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>메트릭 및 로그에 대한 규칙 다시 구성

메트릭 및 로그에 대한 구성 규칙을 변경하려면 왼쪽 창에서 **메트릭 및 로그** 를 선택합니다.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Datadog 리소스에 대한 로그 및 메트릭의 구성을 수정합니다." border="true":::

자세한 내용은 [메트릭 및 로그 구성](create.md#configure-metrics-and-logs)을 참조하세요.

## <a name="view-monitored-resources"></a>모니터링되는 리소스 보기

Datadog에 로그를 보내는 리소스의 목록을 보려면 왼쪽 창에서 **모니터링되는 리소스** 를 선택합니다.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Datadog에서 모니터링되는 리소스 보기" border="true":::

리소스 종류, 리소스 그룹 이름, 위치, 그리고 리소스에서 로그 및 메트릭을 전송하고 있는지를 기준으로 리소스 목록을 필터링할 수 있습니다.

**Logs to Datadog** 열은 리소스가 Datadog에 로그를 보내고 있는지를 나타냅니다. 리소스가 로그를 전송하지 않는 경우 이 필드는 로그가 Datadog에 전송되지 않는 이유를 나타냅니다. 다음과 같은 이유가 있을 수 있습니다.

- 리소스에서 로그 전송을 지원하지 않습니다. 모니터링 로그 범주에 해당하는 리소스 유형만 Datadog로 로그를 보내도록 구성할 수 있습니다.
- 5개의 진단 설정 수 제한에 도달했습니다. 각 Azure 리소스에는 최대 5개의 진단 설정이 있을 수 있습니다. 자세한 내용은 [진단 설정](../../azure-monitor/essentials/diagnostic-settings.md)을 참조하세요.
- 오류. 리소스가 Datadog로 로그를 보내도록 구성되었지만 오류로 인해 차단되었습니다.
- 로그가 구성되지 않았습니다. 적절한 리소스 태그가 있는 Azure 리소스만 Datadog로 로그를 보내도록 구성할 수 있습니다.
- 지원되지 않는 지역입니다. Azure 리소스가 현재 Datadog에 대한 로그 전송을 지원하지 않는 지역에 있습니다.
- Datadog 에이전트가 구성되지 않았습니다. Datadog 에이전트가 설치되지 않은 가상 머신은 Datadog에 로그를 보내지 않습니다.

## <a name="api-keys"></a>API 키

Datadog 리소스에 대한 API 키 목록을 보려면 왼쪽 창에서 **키** 를 선택합니다. 키에 대한 정보가 표시됩니다.

:::image type="content" source="media/manage/keys.png" alt-text="Datadog 조직용 API 키" border="true":::

Azure Portal은 API 키에 대한 읽기 전용 보기를 제공합니다. 키를 관리하려면 Datadog 포털 링크를 선택합니다. Datadog 포털에서 변경한 후 Azure Portal 보기를 새로 고칩니다.

Azure Datadog 통합은 가상 머신 또는 App Service에 Datadog 에이전트를 설치하는 기능을 제공합니다. 기본 키를 선택하지 않으면 Datadog 에이전트 설치에 실패합니다.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Datadog 에이전트를 사용하여 가상 머신 모니터링

가상 머신에 Datadog 에이전트를 확장으로 설치할 수 있습니다. 왼쪽 창의 Datadog 조직 구성에서 **가상 머신 에이전트** 로 이동합니다. 이 화면에는 구독에 있는 모든 가상 머신의 목록이 표시됩니다.

각 가상 머신에 대해 다음 데이터가 표시됩니다.

- 리소스 이름 - 가상 머신 이름
- 리소스 상태 – 가상 머신가 중지되었는지 아니면 실행 중인지를 나타냅니다. Datadog 에이전트는 실행 중인 가상 머신에만 설치할 수 있습니다. 가상 머신이 중지된 경우 Datadog 에이전트를 설치할 수 없습니다.
- 에이전트 버전 – Datadog 에이전트 버전 번호입니다.
- 에이전트 상태 – Datadog 에이전트가 가상 머신에서 실행되고 있는지의 여부입니다.
- 통합 사용 – Datadog 에이전트가 수집하는 주요 메트릭입니다.
- 설치 방법 – Datadog 에이전트 설치에 사용되는 특정 도구입니다. 예를 들면 Chef 또는 스크립트가 있습니다.
- 로그 전송 – Datadog 에이전트가 Datadog에 로그를 보내고 있는지를 나타냅니다.

Datadog 에이전트를 설치할 가상 머신을 선택합니다. **에이전트 설치** 를 선택합니다.

포털에 기본 키로 에이전트를 설치할지 확인하는 메시지가 표시됩니다. **확인** 을 선택하여 설치를 시작합니다. Azure는 에이전트가 설치 및 프로비저닝될 때까지 상태를 **설치 중** 으로 표시합니다.

Datadog 에이전트가 설치되면 상태가 설치됨으로 변경됩니다.

Datadog 에이전트가 설치되어 있는지 확인하려면 가상 머신을 선택하고 확장 창으로 이동합니다.

**가상 머신 에이전트** 로 이동하여 가상 머신에서 Datadog 에이전트를 제거할 수 있습니다. 가상 머신을 선택하고 **에이전트를 제거** 합니다.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Datadog 에이전트를 확장으로 사용하여 App Services 모니터링

Datadog 에이전트를 App Services에 확장으로 설치할 수 있습니다. 왼쪽 창에서 **App Service 확장** 으로 이동합니다. 이 화면에는 구독에 있는 모든 App Services의 목록이 표시됩니다.

각 App Service에 대해 다음 데이터 요소가 표시됩니다.

- 리소스 이름 - 가상 머신 이름입니다.
- 리소스 상태 – App Service가 중지되었는지 아니면 실행 중인지를 나타냅니다. Datadog 에이전트는 실행 중인 App Services에만 설치할 수 있습니다. App Service가 중지된 경우 Datadog 에이전트를 설치할 수 없습니다.
- App Service 요금제 - App Service에 대해 구성된 특정 플랜입니다.
- 에이전트 버전 – Datadog 에이전트 버전 번호입니다.

Datadog 에이전트를 설치하려면 App Service를 선택하고 **확장을 설치** 합니다. 최신 Datadog 에이전트가 App Service에 확장으로 설치됩니다.

포털에서 Datadog 에이전트를 설치할지 확인합니다. 또한 특정 App Service에 대한 애플리케이션 설정은 기본 키로 업데이트됩니다. Datadog 에이전트 설치가 완료되면 App Service가 다시 시작됩니다.

**확인** 을 선택하여 Datadog 에이전트에 대한 설치 프로세스를 시작합니다. 포털은 에이전트가 설치될 때까지 상태를 **설치 중** 으로 표시합니다. Datadog 에이전트가 설치되면 상태가 설치됨으로 변경됩니다.

App Service에서 Datadog 에이전트를 제거하려면 **App Service 확장** 으로 이동합니다. App Service를 선택하고 **확장을 제거** 합니다.

## <a name="reconfigure-single-sign-on"></a>Single Sign-On 다시 구성

Single Sign-On을 다시 구성하려면 왼쪽 창에서 **Single Sign-On** 을 선택합니다.

Azure Active Directory를 통해 Single Sign-On을 설정하려면 **Azure Active Directory를 통해 Single Sign-On 사용** 을 선택합니다.

Portal이 Azure Active Directory에서 적절한 Datadog 애플리케이션을 검색합니다. 앱이 통합을 설정할 때 선택한 엔터프라이즈 앱 이름으로 나옵니다. 아래와 같이 Datadog 앱 이름을 선택합니다.
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Single Sign-On 애플리케이션을 다시 구성합니다." border="true":::
 
## <a name="disable-or-enable-integration"></a>통합 사용 또는 사용 안 함

Azure에서 Datadog로 로그 및 메트릭을 전송하는 것을 중지할 수 있습니다. 메트릭 및 로그 모니터링과 관련되지 않은 다른 Datadog 서비스에 대한 요금이 계속 청구됩니다.

Datadog와의 Azure 통합을 사용하지 않도록 설정하려면 **개요** 로 이동합니다. **사용 안 함** 을 선택하고 **확인** 을 선택합니다.
 
:::image type="content" source="media/manage/disable.png" alt-text="Datadog 리소스를 사용하지 않도록 설정합니다." border="true":::

Datadog와의 Azure 통합을 사용하도록 설정하려면 **개요** 로 이동합니다. **사용** 을 선택하고 **확인** 을 선택합니다. **사용** 을 선택하면 메트릭 및 로그에 대한 이전 구성을 가져옵니다. 구성에 따라 Datadog에 대한 메트릭 및 로그를 내보내는 Azure 리소스가 결정됩니다. 단계를 완료하면 메트릭 및 로그가 Datadog로 전송됩니다.
 
:::image type="content" source="media/manage/enable.png" alt-text="Datadog 리소스를 사용하도록 설정합니다." border="true":::

## <a name="delete-datadog-resource"></a>Datadog 리소스 삭제

왼쪽 창에서 **개요** 로 이동하고 **삭제** 를 선택합니다. Datadog 리소스를 삭제할 것인지 확인합니다. **삭제** 를 선택합니다.

:::image type="content" source="media/manage/delete.png" alt-text="Datadog 리소스 삭제" border="true":::

Datadog 조직에 Datadog 리소스를 하나만 매핑하면 로그와 메트릭이 더 이상 Datadog로 전송되지 않습니다. Azure Marketplace을 통한 모든 Datadog 관련 청구가 중지됩니다.

둘 이상의 Datadog 리소스가 Datadog 조직에 매핑되는 경우 Datadog 리소스를 삭제하면 해당 Datadog 리소스에 대한 로그 및 메트릭 전송만 중지됩니다. Datadog 조직이 다른 Azure 리소스에 연결되어 있으므로 Azure Marketplace를 통해 계속 청구됩니다.

## <a name="getting-support"></a>지원 받기

Azure Datadog 통합에 대해 고객 지원팀에 문의하려면 왼쪽 창에서 **새 지원 요청** 을 선택합니다. Datadog 포털 링크를 선택합니다.

:::image type="content" source="media/manage/support-request.png" alt-text="새 Datadog 지원 요청 만들기" border="true":::

## <a name="next-steps"></a>다음 단계

문제 해결에 대한 도움말은 [Datadog 솔루션 문제 해결](troubleshoot.md)을 참조하세요.