---
title: Datadog 리소스 관리-Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal의 Datadog 리소스 관리에 대해 설명 합니다. Single Sign-On를 설정 하 고, Confluent 조직을 삭제 하 고, 지원을 받는 방법
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 1a76f79f31d1f4518c069afb7fccbad5bd22d4e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745296"
---
# <a name="manage-the-datadog-resource"></a>Datadog 리소스 관리

이 문서에서는 Datadog와 Azure 통합에 대 한 설정을 관리 하는 방법을 보여 줍니다.

## <a name="resource-overview"></a>리소스 개요

Datadog 리소스에 대 한 세부 정보를 보려면 왼쪽 창에서 **개요** 를 선택 합니다.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Datadog 리소스 개요" border="true" lightbox="media/manage/resource-overview.png":::

세부 정보는 다음과 같습니다.

- 리소스 그룹 이름
- 위치/지역
- Subscription
- 태그
- Datadog 조직에 대 한 Single sign-on 링크
- Datadog 제품/계획
- 청구 기간

또한 Datadog 대시보드, 로그 및 호스트 맵에 대 한 링크도 제공 합니다.

개요 화면에서는 Datadog에 로그 및 메트릭을 보내는 리소스에 대 한 요약 정보를 제공 합니다.

- 리소스 종류 – Azure 리소스 종류입니다.
- 총 리소스 수-리소스 종류에 대 한 모든 리소스의 수입니다.
- 로그 전송 리소스 – 통합을 통해 Datadog로 로그를 전송 하는 리소스의 수입니다.
- 리소스 전송 메트릭 – 통합을 통해 Datadog 메트릭을 전송 하는 리소스의 수입니다.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>메트릭 및 로그에 대 한 규칙 다시 구성

메트릭 및 로그에 대 한 구성 규칙을 변경 하려면 왼쪽 창에서 **메트릭 및 로그** 를 선택 합니다.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Datadog 리소스에 대 한 로그 및 메트릭의 구성을 수정 합니다." border="true":::

자세한 내용은 [메트릭 및 로그 구성](create.md#configure-metrics-and-logs)을 참조 하세요.

## <a name="view-monitored-resources"></a>모니터링 되는 리소스 보기

Datadog에 로그를 내보내는 리소스의 목록을 보려면 왼쪽 창에서 **모니터링** 되는 리소스를 선택 합니다.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Datadog에서 모니터링 하는 리소스 보기" border="true":::

리소스 종류, 리소스 그룹 이름, 위치 및 리소스에서 로그 및 메트릭을 전송 하 고 있는지 여부를 기준으로 리소스 목록을 필터링 할 수 있습니다.

**Datadog에 대** 한 열 로그는 리소스가 Datadog에 로그를 보내고 있는지 여부를 나타냅니다. 리소스가 로그를 전송 하지 않는 경우이 필드는 로그가 Datadog 전송 되지 않는 이유를 나타냅니다. 원인은 다음과 같습니다.

- 리소스에서 로그 전송을 지원 하지 않습니다. Datadog로 로그를 보내도록 모니터링 로그 범주의 리소스 유형만 구성할 수 있습니다.
- 진단 설정 5 개로 제한에 도달 했습니다. 각 Azure 리소스에는 최대 5 개의 진단 설정이 있을 수 있습니다. 자세한 내용은 [진단 설정](../../azure-monitor/platform/diagnostic-settings.md)을 참조 하세요.
- 오류. 리소스가 Datadog로 로그를 보내도록 구성 되었지만 오류로 인해 차단 되었습니다.
- 로그가 구성 되지 않았습니다. 적절 한 리소스 태그가 있는 Azure 리소스만 Datadog로 로그를 보내도록 구성 됩니다.
- 지역이 지원 되지 않습니다. Azure 리소스는 현재 Datadog에 대 한 로그 전송을 지원 하지 않는 지역에 있습니다.
- Datadog 에이전트가 구성 되지 않았습니다. Datadog 에이전트가 설치 되지 않은 가상 머신은 Datadog에 로그를 내보내지 않습니다.

## <a name="api-keys"></a>API 키

Datadog 리소스에 대 한 API 키 목록을 보려면 왼쪽 창에서 **키** 를 선택 합니다. 키에 대 한 정보가 표시 됩니다.

:::image type="content" source="media/manage/keys.png" alt-text="Datadog 구성에 대 한 API 키" border="true":::

Azure Portal은 API 키에 대 한 읽기 전용 보기를 제공 합니다. 키를 관리 하려면 Datadog 포털 링크를 선택 합니다. Datadog 포털에서 변경한 후 Azure Portal 보기를 새로 고칩니다.

Azure Datadog 통합은 가상 머신 또는 app service에 Datadog 에이전트를 설치 하는 기능을 제공 합니다. 기본 키를 선택 하지 않으면 Datadog 에이전트 설치에 실패 합니다.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Datadog 에이전트를 사용 하 여 가상 머신 모니터링

가상 컴퓨터에 Datadog 에이전트를 확장으로 설치할 수 있습니다. 왼쪽 창의 Datadog org 구성에서 **가상 컴퓨터 에이전트로** 이동 합니다. 이 화면에는 구독에 있는 모든 가상 컴퓨터의 목록이 표시 됩니다.

각 가상 컴퓨터에 대해 다음 데이터가 표시 됩니다.

- 리소스 이름-가상 머신 이름
- 리소스 상태 – 가상 컴퓨터가 중지 되었는지 아니면 실행 중인지를 나타냅니다. Datadog 에이전트는를 실행 하는 가상 컴퓨터에만 설치할 수 있습니다. 가상 컴퓨터가 중지 된 경우 Datadog 에이전트를 설치 하지 못합니다.
- 에이전트 버전 – Datadog 에이전트 버전 번호입니다.
- 에이전트 상태 – Datadog 에이전트가 가상 머신에서 실행 되 고 있는지 여부입니다.
- 통합 사용 – Datadog 에이전트가 수집 하는 주요 메트릭입니다.
- Install Method – Datadog agent를 설치 하는 데 사용 되는 특정 도구입니다. 예를 들면 Chef 또는 Script가 있습니다.
- 로그 보내기 – Datadog 에이전트가 Datadog에 로그를 보내고 있는지 여부를 나타냅니다.

Datadog 에이전트를 설치할 가상 컴퓨터를 선택 합니다. **에이전트 설치** 를 선택 합니다.

포털에서 기본 키를 사용 하 여 에이전트를 설치할지 확인 하는 메시지를 표시 합니다. **확인** 을 선택 하 여 설치를 시작 합니다. Azure는 에이전트가 설치 및 프로 비전 될 때까지 **설치 중** 상태를 표시 합니다.

Datadog 에이전트가 설치 되 면 상태가 설치 됨으로 변경 됩니다.

Datadog 에이전트가 설치 되어 있는지 확인 하려면 가상 컴퓨터를 선택 하 고 확장 창으로 이동 합니다.

가상 컴퓨터 **에이전트** 로 이동 하 여 가상 컴퓨터에서 Datadog 에이전트를 제거할 수 있습니다. 가상 컴퓨터를 선택 하 고 **에이전트를 제거** 합니다.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Datadog 에이전트를 확장으로 사용 하 여 App Services 모니터링

Datadog 에이전트를 app services에 확장으로 설치할 수 있습니다. 왼쪽 창에서 **App Service 확장** 으로 이동 합니다. 이 화면에는 구독에 있는 모든 앱 서비스의 목록이 표시 됩니다.

각 app service에 대해 다음과 같은 데이터 요소가 표시 됩니다.

- 리소스 이름-가상 머신 이름입니다.
- 리소스 상태 – app service가 중지 되었는지 아니면 실행 중인지를 나타냅니다. Datadog 에이전트는를 실행 하는 app services에만 설치할 수 있습니다. App service가 중지 된 경우 Datadog 에이전트 설치를 사용할 수 없습니다.
- App service 계획-app service에 대해 구성 된 특정 계획입니다.
- 에이전트 버전 – Datadog 에이전트 버전 번호입니다.

Datadog 에이전트를 설치 하려면 app service를 선택 하 고 **확장을 설치** 합니다. 최신 Datadog 에이전트는 app service에 확장으로 설치 됩니다.

포털에서 Datadog 에이전트를 설치할지 확인 합니다. 또한 특정 app service에 대 한 응용 프로그램 설정은 기본 키로 업데이트 됩니다. Datadog 에이전트 설치가 완료 되 면 app service가 다시 시작 됩니다.

**확인** 을 선택 하 여 Datadog 에이전트에 대 한 설치 프로세스를 시작 합니다. 포털은 에이전트가 설치 될 때까지 **설치 중** 상태를 표시 합니다. Datadog 에이전트가 설치 되 면 상태가 설치 됨으로 변경 됩니다.

App service에서 Datadog agent를 제거 하려면 **App Service 확장** 으로 이동 합니다. App service를 선택 하 고 **확장을 제거** 합니다.

## <a name="reconfigure-single-sign-on"></a>다시 구성 Single Sign-On

Single Sign-On를 다시 구성 하려면 왼쪽 창에서 **single sign-on** 을 선택 합니다.

Azure Active directory를 통해 Single Sign-On을 설정 하려면 **Azure Active Directory를 통해 Single Sign-On 사용 하도록 설정** 을 선택 합니다.

포털은 Azure Active Directory에서 적절 한 Datadog 응용 프로그램을 검색 합니다. 앱은 통합을 설정할 때 선택한 엔터프라이즈 앱 이름에서 제공 됩니다. 아래와 같이 Datadog 앱 이름을 선택 합니다.
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Single Sign-On 응용 프로그램을 다시 구성 합니다." border="true":::
 
## <a name="disable-or-enable-integration"></a>통합 사용 또는 사용 안 함

Azure에서 Datadog로 로그 및 메트릭을 전송 하는 것을 중지할 수 있습니다. 메트릭 및 로그 모니터링과 관련 되지 않은 다른 Datadog 서비스에 대 한 요금이 계속 청구 됩니다.

Datadog와 Azure 통합을 사용 하지 않도록 설정 하려면 **개요** 로 이동 합니다. **사용 안 함** 을 선택 하 고 **확인** 을 선택 합니다.
 
:::image type="content" source="media/manage/disable.png" alt-text="Datadog 리소스를 사용 하지 않도록 설정 합니다." border="true":::

Datadog와 Azure 통합을 사용 하도록 설정 하려면 **개요** 로 이동 합니다. **사용** 을 선택 하 고 **확인** 을 선택 합니다. **사용** 을 선택 하면 메트릭 및 로그에 대 한 이전 구성이 검색 됩니다. 구성에 따라 Datadog에 대 한 메트릭 및 로그를 내보내는 Azure 리소스가 결정 됩니다. 단계를 완료 하면 메트릭 및 로그가 Datadog으로 전송 됩니다.
 
:::image type="content" source="media/manage/enable.png" alt-text="Datadog 리소스를 사용 하도록 설정 합니다." border="true":::

## <a name="delete-datadog-resource"></a>Datadog 리소스 삭제

왼쪽 창에서 **개요** 로 이동 하 고 **삭제** 를 선택 합니다. Datadog 리소스를 삭제할 것인지 확인 합니다. **삭제** 를 선택합니다.

:::image type="content" source="media/manage/delete.png" alt-text="Datadog 리소스 삭제" border="true":::

Datadog 조직에 Datadog 리소스를 하나만 매핑하면 로그와 메트릭이 더 이상 Datadog로 전송 되지 않습니다. 모든 청구는 Azure Marketplace을 통해 Datadog를 중지 합니다.

둘 이상의 Datadog 리소스가 Datadog 조직에 매핑되는 경우 Datadog 리소스를 삭제 하면 해당 Datadog 리소스에 대 한 로그 및 메트릭 전송만 중지 됩니다. Datadog 조직은 다른 Azure 리소스에 연결 되어 있기 때문에 Azure Marketplace 계속 청구 됩니다.

## <a name="getting-support"></a>지원 받기

Azure Datadog 통합에 대 한 지원에 문의 하려면 왼쪽 창에서 **새 지원 요청** 를 선택 합니다. Datadog 포털에 대 한 링크를 선택 합니다.

:::image type="content" source="media/manage/support-request.png" alt-text="새 Datadog 지원 요청 만들기" border="true":::

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 도움말은 [Datadog 솔루션 문제 해결](troubleshoot.md)을 참조 하세요.
