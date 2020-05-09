---
title: Operations Manager와 VM용 Azure Monitor Map 통합 | Microsoft Docs
description: VM용 Azure Monitor는 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색 하 고 서비스 간 통신을 매핑합니다. 이 문서에서는 맵 기능을 사용 하 여 Operations Manager에서 배포 응용 프로그램 다이어그램을 자동으로 만드는 방법을 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: a20884c33d5232661e858e0b3ec722f139825aea
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82928009"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>VM용 Azure Monitor Map 기능과 System Center Operations Manager 통합

VM용 Azure Monitor에서 Azure 또는 사용자 환경에서 실행 되는 Windows 및 Linux Vm (가상 머신)에서 검색 된 응용 프로그램 구성 요소를 볼 수 있습니다. 지도 기능과 System Center Operations Manager 간의 이러한 통합을 통해 VM용 Azure Monitor의 동적 종속성 맵을 기반으로 하는 Operations Manager에서 분산 응용 프로그램 다이어그램을 자동으로 만들 수 있습니다. 이 문서에서는이 기능을 지원 하도록 System Center Operations Manager 관리 그룹을 구성 하는 방법을 설명 합니다.

>[!NOTE]
>이미 서비스 맵를 배포한 경우 VM 상태 및 성능을 모니터링 하는 추가 기능을 포함 하는 VM용 Azure Monitor에서 지도를 볼 수 있습니다. VM용 Azure Monitor의 맵 기능은 독립 실행형 서비스 맵 솔루션을 대체 하기 위한 것입니다. 자세한 내용은 [VM용 Azure Monitor 개요](vminsights-overview.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* System Center Operations Manager 관리 그룹 (2012 R2 이상)
* VM용 Azure Monitor을 지원 하도록 구성 된 Log Analytics 작업 영역입니다.
* Operations Manager 하 여 모니터링 하 고 Log Analytics 작업 영역에 데이터를 전송 하는 하나 이상의 Windows 및 Linux 가상 머신 또는 물리적 컴퓨터입니다. Operations Manager 관리 그룹에 보고 하는 Linux 서버를 Azure Monitor에 직접 연결 하도록 구성 해야 합니다. 자세한 내용은 [Log Analytics 에이전트를 사용 하 여 로그 데이터 수집](../platform/log-analytics-agent.md)의 개요를 참조 하세요.
* Log Analytics 작업 영역과 연결된 Azure 구독에 액세스할 수 있는 서비스 주체. 자세한 내용은 [서비스 주체 만들기](#create-a-service-principal)로 이동합니다.

## <a name="install-the-service-map-management-pack"></a>서비스 맵 관리 팩 설치

SystemCenter 관리 팩 번들 (SystemCenter .mpb)을 가져와서 Operations Manager와 맵 기능 간의 통합을 사용 하도록 설정 합니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=55763)에서 관리 팩 번들을 다운로드할 수 있습니다. 이 번들에는 다음 관리 팩이 포함됩니다.

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>통합 구성

서비스 맵 관리 팩을 설치 하면 Operations Manager 운영 콘솔의 **관리** 창에서 새 노드 **서비스 맵**가 **operations management Suite** 에 표시 됩니다.

>[!NOTE]
>[Operations Management Suite](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) 는 Log Analytics 포함 된 서비스의 모음으로, 이제 [Azure Monitor](../overview.md)의 일부입니다.

VM용 Azure Monitor 맵 통합을 구성 하려면 다음을 수행 합니다.

1. 구성 마법사를 열려면 **서비스 맵 개요** 창에서 **작업 영역 추가**를 클릭합니다.  

    ![서비스 맵 개요 창](media/service-map-scom/scom-configuration.png)

2. **연결 구성** 창에서 서비스 주체의 테넌트 이름 또는 ID, 애플리케이션 ID(사용자 이름 또는 클라이언트 ID라고도 함) 및 암호를 입력한 후, **다음**을 클릭합니다. 자세한 내용은 서비스 주체 만들기를 참조하세요.

    ![연결 구성 창](media/service-map-scom/scom-config-spn.png)

3. **구독 선택** 창에서 Azure 구독, Azure 리소스 그룹(Log Analytics 작업 영역을 포함하는 그룹), Log Analytics 작업 영역을 선택한 후, **다음**을 클릭합니다.

    ![Operations Manager 구성 작업 영역](media/service-map-scom/scom-config-workspace.png)

4. **컴퓨터 그룹 선택** 창에서 Operations Manager에 동기화하려는 [서비스 맵 컴퓨터 그룹]을 선택합니다. **컴퓨터 그룹 추가/제거**를 클릭하고, **사용 가능한 컴퓨터 그룹** 목록에서 그룹을 선택하고, **추가**를 클릭합니다.  그룹 선택이 완료되면 **확인**을 클릭하여 완료합니다.

    ![Operations Manager 구성 컴퓨터 그룹](media/service-map-scom/scom-config-machine-groups.png)

5. **서버 선택** 창에서 Operations Manager와 맵 기능 간에 동기화 할 서버를 사용 하 여 서비스 맵 servers 그룹을 구성 합니다. **서버 추가/제거**를 클릭합니다.

    통합 기능을 통해 서버에 대한 분산 애플리케이션 다이어그램을 만들려면 서버가 다음 조건을 충족해야 합니다.

   * Operations Manager 모니터링
   * VM용 Azure Monitor 구성 된 Log Analytics 작업 영역에 보고 하도록 구성 됨
   * 서비스 맵 서버 그룹에 나열됨

     ![Operations Manager 구성 그룹](media/service-map-scom/scom-config-group.png)

6. 선택 사항: Log Analytics와 통신할 모든 관리 서버 리소스 풀을 선택한 다음 **작업 영역 추가**를 클릭 합니다.

    ![Operations Manager 구성 리소스 풀](media/service-map-scom/scom-config-pool.png)

    Log Analytics 작업 영역을 구성 및 등록하는 데 1분 정도 걸릴 수 있습니다. 구성 된 후에는 Operations Manager 첫 번째 맵 동기화를 시작 합니다.

    ![Operations Manager 구성 리소스 풀](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>통합 모니터링

Log Analytics 작업 영역이 연결 되 면 Operations Manager 운영 콘솔의 **모니터링** 창에 서비스 맵 새 폴더가 표시 됩니다.

![Operations Manager 모니터링 창](media/service-map-scom/scom-monitoring.png)

서비스 맵 폴더에는 4개의 노드가 있습니다.

* **활성 경고**: Operations Manager와 Azure Monitor 간의 통신에 대 한 모든 활성 경고를 나열 합니다.  

  >[!NOTE]
  >이러한 경고는 Operations Manager와 동기화 된 Log Analytics 경고가 아닙니다. 서비스 맵 관리 팩에 정의 된 워크플로에 따라 관리 그룹에 생성 됩니다.

* **서버**: VM용 Azure Monitor 지도 기능에서 동기화 하도록 구성 된 모니터링 대상 서버를 나열 합니다.

    ![Operations Manager 서버 모니터링 창](media/service-map-scom/scom-monitoring-servers.png)

* **컴퓨터 그룹 종속성 보기**: 맵 기능에서 동기화 된 모든 컴퓨터 그룹을 나열 합니다. 원하는 그룹을 클릭하여 해당 그룹의 배포 애플리케이션 다이어그램을 볼 수 있습니다.

    ![Operations Manager 분산 애플리케이션 다이어그램](media/service-map-scom/scom-group-dad.png)

* **서버 종속성 보기**: 맵 기능에서 동기화 되는 모든 서버를 나열 합니다. 원하는 서버를 클릭하면 해당 서버의 분산 애플리케이션 다이어그램을 볼 수 있습니다.

    ![Operations Manager 분산 애플리케이션 다이어그램](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>작업 영역 편집 또는 삭제

**서비스 맵 개요** 창(**관리** 창 --> Operations Management Suite**Operations Management Suite** > **서비스 맵**)을 통해 구성된 작업 영역을 편집하거나 삭제할 수 있습니다.

> [!NOTE]
> [Operations Management Suite은 서비스 컬렉션](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)로, Log Analytics를 포함했으며 현재는 [Azure Monitor](../overview.md)의 일부입니다.

현재 릴리스에서는 Log Analytics 작업 영역을 하나만 구성할 수 있습니다.

![Operations Manager 작업 영역 편집 창](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>규칙 및 재정의 구성

*SystemCenter microsoft.systemcenter.servicemapimport.rule*는 VM용 Azure Monitor 맵 기능에서 정보를 주기적으로 가져옵니다. 동기화 간격을 수정 하려면 규칙을 재정의 하 고 매개 변수 값을 **Intminutes**로 수정할 수 있습니다.

![Operations Manager 재정의 속성 창](media/service-map-scom/scom-overrides.png)

* **Enabled** – 자동 업데이트를 사용하거나 사용하지 않도록 설정합니다.
* **Intminutes**: 업데이트 사이의 시간을 지정 합니다. 기본 간격은 1시간입니다. 맵을 더 자주 동기화 하려는 경우 값을 변경할 수 있습니다.
* **Timeoutseconds**: 요청 시간이 초과 되기까지의 시간을 지정 합니다.
* **Timewindowminutes**: 데이터를 쿼리 하는 기간을 지정 합니다. 기본값은 허용 되는 최대 간격인 60 분입니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

현재 디자인은 다음과 같은 문제 및 제한 사항을 나타냅니다.

* 하나의 Log Analytics 작업 영역에만 연결할 수 있습니다.
* **제작** 창을 통해 서비스 맵 서버 그룹에 서버를 수동으로 추가할 수는 있지만, 이러한 서버에 대한 맵은 즉시 동기화되지 않으며 이러한 작업은 다음 동기화 주기 동안 VM용 Azure Monitor 맵 기능에서 동기화 됩니다.
* 관리 팩에서 만든 배포 응용 프로그램 다이어그램을 변경 하는 경우 다음 동기화를 VM용 Azure Monitor와 동기화 할 때 이러한 변경 내용을 덮어쓸 수 있습니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

서비스 주체 만들기에 대한 공식적인 Azure 설명서를 보려면 다음을 참조하세요.

* [PowerShell을 사용하여 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI를 사용하여 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure Portal을 사용하여 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>사용자 의견
VM용 Azure Monitor Map 기능 또는이 설명서와의 통합에 대 한 의견이 있나요? 기능을 제안하거나 기존 제안에 투표할 수 있는 [사용자 의견 페이지](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)를 방문하세요.
