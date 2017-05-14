---
title: "System Center Operations Manager와 서비스 맵 통합 | Microsoft Docs"
description: "서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하여 서비스 간 통신을 매핑하는 Operations Management Suite 솔루션입니다. 이 문서에서는 서비스 맵을 사용하여 Operations Manager에 자동으로 분산 응용 프로그램 다이어그램을 만드는 방법을 설명합니다."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0b710c338be3a2c2fde6bba43173f7c5f480e357
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---

# <a name="service-map-integration-with-system-center-operations-manager"></a>System Center Operations Manager와 서비스 맵 통합
  > [!NOTE]
  > 이 기능은 비공개 미리 보기에 있으므로 프로덕션 시스템에서 사용하지 않아야 합니다.
  > 
  
Operations Management Suite 서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하여 서비스 간 통신을 매핑합니다. 서비스 맵을 사용하면 생각하는 방식대로 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다. 서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다. 자세한 내용은 [서비스 맵 설명서](operations-management-suite-service-map.md)를 참조하세요.

서비스 맵과 System Center Operations Manager 간의 이러한 통합을 통해 서비스 맵의 동적 종속성 맵을 기준으로 하는 분산 응용 프로그램 다이어그램을 Operations Manager에서 자동으로 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* 일련의 서버를 관리하는 Operations Manager 관리 그룹
* 서비스 맵 솔루션이 사용하도록 설정된 Operations Management Suite 작업 영역
* Operations Manager로 관리되고 있고 서비스 맵으로 데이터를 전송하는 서버 집합(하나 이상의 서버) Windows 및 Linux 서버가 지원됩니다.
* Operations Management Suite 작업 영역과 연결된 Azure 구독에 액세스할 수 있는 서비스 주체 자세한 내용은 [서비스 주체 만들기](#creating-a-service-principal)로 이동합니다.

## <a name="install-the-service-map-management-pack"></a>서비스 맵 관리 팩 설치
Operations Manager와 서비스 맵의 통합은 Microsoft.SystemCenter.ServiceMap 관리 팩 번들(Microsoft.SystemCenter.ServiceMap.mpb)을 가져와야 가능합니다. 이 번들에는 다음 관리 팩이 포함됩니다.
* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>서비스 맵 통합 구성
서비스 맵 관리 팩을 설치하면 **관리** 창의 **Operations Management Suite** 아래에 **Service Map**이라는 새 노드가 표시됩니다. 

서비스 맵 통합을 구성하려면 다음을 수행합니다.

1. 구성 마법사를 열려면 **서비스 맵 개요** 창에서 **작업 영역 추가**를 클릭합니다.  

    ![서비스 맵 개요 창](media/oms-service-map/scom-configuration.png)

2. **연결 구성** 창에서 서비스 주체의 테넌트 이름 또는 ID, 응용 프로그램 ID(사용자 이름 또는 클라이언트 ID라고도 함) 및 암호를 입력하고 **다음**을 클릭합니다. 자세한 내용은 [서비스 주체 만들기](#creating-a-service-principal)로 이동합니다.

    ![연결 구성 창](media/oms-service-map/scom-config-spn.png)

3. **구독 선택** 창에서 Azure 구독, Azure 리소스 그룹(Operations Management Suite를 포함하는 그룹), Operations Management Suite 작업 영역을 선택하고 **다음**을 클릭합니다.

    ![Operations Manager 구성 작업 영역](media/oms-service-map/scom-config-workspace.png)

4. **서버 선택** 창에서 Operations Manager 및 서비스 맵 간에 동기화하려는 서버가 있는 서비스 맵 서버 그룹을 구성합니다. **서버 추가/제거**를 클릭합니다.   
    
    통합 기능을 통해 서버에 대한 분산 응용 프로그램 다이어그램을 만들려면 서버가 다음 조건을 충족해야 합니다.

    * Operations Manager를 통해 관리됩니다.
    * 서비스 맵을 통해 관리됩니다.
    * 서비스 맵 서버 그룹에 표시됩니다.

    ![Operations Manager 구성 그룹](media/oms-service-map/scom-config-group.png)

5. 선택 사항 - Operations Management Suite와 통신할 관리 서버 리소스 풀을 선택하고 **작업 영역 추가**를 클릭합니다.

    ![Operations Manager 구성 리소스 풀](media/oms-service-map/scom-config-pool.png)

    Operations Management Suite 작업 영역을 구성 및 등록하는 데 1분 정도 걸릴 수 있습니다. 구성된 후에 Operations Manager는 Operations Management Suite의 첫 번째 서비스 맵 동기화를 시작합니다.

    ![Operations Manager 구성 리소스 풀](media/oms-service-map/scom-config-success.png)

    >[!NOTE]
    >기본 동기화 간격은 60분으로 설정됩니다. 재정의를 구성하여 동기화 간격을 변경할 수 있습니다. **제작** 창에서 수동으로 서비스 맵 서버 그룹에 서버를 추가할 수도 있습니다. 이렇게 하려면 **그룹**을 선택하고 **서비스 맵 서버 그룹**을 검색합니다. 이러한 서버에 대한 서버 맵은 구성된 동기화 간격에 따라 다음 동기화와 동기화됩니다.

## <a name="monitor-service-map"></a>서비스 맵 모니터링
Operations Management Suite 작업 영역이 연결되면 새 폴더인 Service Map이 Operations Manager 콘솔의 **모니터링** 창에 표시됩니다.

![Operations Manager 모니터링 창](media/oms-service-map/scom-monitoring.png)

서비스 맵 폴더에는 3개의 노드가 있습니다.
* **활성 경고**: Operations Management Suite의 Operations Manager 및 서비스 맵 간 통신에 대한 모든 활성 경고를 표시합니다.

    >[!NOTE]
    >이러한 경고는 Operations Manager에 표시되는 Operations Management Suite 경고가 아닙니다.

* **서버**: 서비스 맵에서 동기화하도록 구성된 모니터링 대상 서버를 나열합니다.

    ![Operations Manager 서버 모니터링 창](media/oms-service-map/scom-monitoring-servers.png)

* **서버 종속성 보기**: 서비스 맵에서 동기화되는 모든 서버를 나열합니다. 원하는 서버를 클릭하면 해당 서버의 분산 응용 프로그램 다이어그램을 볼 수 있습니다.

    ![Operations Manager 분산 응용 프로그램 다이어그램](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>작업 영역 편집 또는 삭제
**서비스 맵 개요** 창(**관리** 창 --> Operations Management Suite**Operations Management Suite** > **서비스 맵**)을 통해 구성된 작업 영역을 편집하거나 삭제할 수 있습니다. 지금은 하나의 Operations Management Suite 작업 영역만 구성할 수 있습니다.

![Operations Manager 작업 영역 편집 창](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>규칙 및 재정의 구성
_Microsoft.SystemCenter.ServiceMapImport.Rule_ 규칙은 서비스 맵에서 주기적으로 정보를 가져오기 위해 생성됩니다. 동기화 타이밍을 변경하려면 규칙 재정의를 구성할 수 있습니다(**제작** 창 > **규칙** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Operations Manager 재정의 속성 창](media/oms-service-map/scom-overrides.png)

* **Enabled** – 자동 업데이트를 사용하거나 사용하지 않도록 설정합니다. 
* **IntervalMinutes**: 업데이트 간 시간을 다시 설정합니다. 기본 간격은 1시간입니다. 서버 맵을 더 자주 동기화하려면 값을 변경할 수 있습니다.
* **TimeoutSeconds**: 요청 시간이 초과되기 전의 시간을 다시 설정합니다. 
* **TimeWindowMinutes**: 데이터를 쿼리하기 위한 기간을 다시 설정합니다. 기본값은 60분입니다. 서비스 맵에서 허용되는 최대값은 60분입니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

현재 디자인은 다음과 같은 문제 및 제한 사항을 나타냅니다.
* **제작** 창을 통해 수동으로 서비스 맵 서버 그룹에 서버를 추가할 수 있지만 이러한 서버에 대한 맵은 다음 동기화 주기 동안에만 서비스 맵에서 동기화됩니다. 기본 설정은 60분이지만 이 타이밍을 재정의할 수 있습니다. 
* 단일 Operations Management Suite 작업 영역에 연결할 수 있습니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기
서비스 주체 만들기에 대한 공식적인 Azure 설명서를 보려면 다음을 참조하세요.
* [PowerShell을 사용하여 서비스 주체 만들기](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI를 사용하여 서비스 주체 만들기](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure Portal을 사용하여 서비스 주체 만들기](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>사용자 의견
서비스 맵 또는 이 설명서에 대한 의견이 있습니까? 기능을 제안하거나 기존 제안에 투표할 수 있는 [사용자 의견 페이지](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)를 방문하세요.

