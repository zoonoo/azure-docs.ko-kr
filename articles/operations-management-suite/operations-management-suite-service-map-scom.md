---
title: "System Center Operations Manager와 서비스 맵 통합 | Microsoft Docs"
description: "서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하여 서비스 간 통신을 매핑하는 OMS(Operations Management Suite) 솔루션입니다.  이 문서는 서비스 맵을 사용하여 SCOM에 자동으로 분산 응용 프로그램 다이어그램을 만드는 방법에 대한 세부 정보를 제공합니다."
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
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 1937462eef4647b273dfa029c8f18c80d3443ae8
ms.lasthandoff: 04/14/2017


---

# <a name="service-map-integration-with-system-center-operations-manager-integration"></a>System Center Operations Manager와 서비스 맵 통합
  > [!NOTE]
  > 이 기능은 비공개 미리 보기에 있으므로 프로덕션 시스템에서 사용하지 않아야 합니다.
  > 
  
Operations Management Suite(OMS) 서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하여 서비스 간 통신을 매핑합니다. 따라서 생각처럼 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다. 서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다.  자세한 내용은 [서비스 맵 설명서](operations-management-suite-service-map.md)를 참조하세요.

서비스 맵과 SCOM(System Center Operations Manager) 간 이러한 통합을 통해 서비스 맵의 동적 종속성 맵을 기반으로 SCOM에서 분산 응용 프로그램 다이어그램을 자동으로 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건
1.    일련의 서버를 관리하는 SCOM 관리 그룹.
2.    서비스 맵 솔루션이 활성화된 OMS 작업 영역.
3.    SCOM으로 관리되고 있고 서비스 맵으로 데이터를 전송하는 일련의 서버(한 대 이상의 서버).  Windows 및 Linux 서버가 지원됩니다.
4.    OMS 작업 영역과 연결된 Azure 구독에 액세스할 수 있는 서비스 주체.  [서비스 주체 만들기에 대한 자세한 내용](#creating-a-service-principal)을 참조하세요.

## <a name="installing-service-map-management-pack"></a>서비스 맵 관리 팩 설치
SCOM과 서비스 맵의 통합은 Microsoft.SystemCenter.ServiceMap 관리 팩 번들(Microsoft.SystemCenter.ServiceMap.mpb)을 가져와야 가능합니다.  이 번들에는 다음 관리 팩이 포함됩니다.
* Microsoft ServiceMap Application Views
* Microsoft System Center ServiceMap Internal
* Microsoft System Center ServiceMap Overrides
* Microsoft System Center ServiceMap

## <a name="configuring-the-service-map-integration"></a>서비스 맵 통합 구성
1. ServiceMap 관리 팩을 설치하면 관리 창의 Operations Management Suite 아래에 Service Map이라는 새 노드가 생깁니다.
2. 구성 마법사를 열려면 서비스 맵 개요 창에서 "작업 영역 추가"를 클릭합니다.

    ![SCOM 구성 마법사](media/oms-service-map/scom-configuration.png)

3. 마법사의 첫 번째 단계는 Azure 서비스 주체에 대한 정보를 입력하는 연결 구성입니다. 서비스 주체의 테넌트 이름 또는 ID, 응용 프로그램 ID(사용자 이름 또는 클라이언트 ID라고도 함) 및 암호를 입력하세요.  [서비스 주체 만들기에 대한 자세한 내용](#creating-a-service-principal)을 참조하세요.

    ![SCOM SPN 구성](media/oms-service-map/scom-config-spn.png)

4. 다음 단계는 Azure 구독, Azure 리소스 그룹(OMS 작업 공간 하나 포함)과 OMS 작업 영역을 선택하는 것입니다.

    ![SCOM 작업 영역 구성](media/oms-service-map/scom-config-workspace.png)

5. 다음 단계는 SCOM과 서비스 맵 간에 동기화하려는 서버와 서비스 맵 서버 그룹을 구성하는 것입니다.  서버 추가/제거… 단추를 클릭합니다. 서버에 대한 분산 응용 프로그램 다이어그램을 만드는 통합의 경우 서버가 1) SCOM으로 관리되고, 2) 서비스 맵으로 관리되고, 3) 서비스 맵 서버 그룹에 등록되어 있어야 합니다.

    ![SCOM 그룹 구성](media/oms-service-map/scom-config-group.png)

6. 선택 사항 - OMS와 통신할 관리 서버 리소스 풀을 선택하고 "작업 영역 추가"를 클릭합니다.

    ![SCOM 리소스 풀 구성](media/oms-service-map/scom-config-pool.png)

7. OMS 작업 영역을 구성하고 등록하려면 1분 정도 걸립니다. 구성이 완료되면 SCOM이 OMS에서 첫 번째 서비스 맵 동기화를 시작합니다.

    ![SCOM 리소스 풀 구성](media/oms-service-map/scom-config-success.png)

**참고:** 기본 동기화 간격은 60분으로 설정됩니다. 사용자는 재정의를 구성하여 동기화 간격을 변경할 수 있습니다. 작성 창을 통해 수동으로 서비스 맵 서버 그룹에 서버를 추가할 수도 있습니다(작성 창 --> 그룹으로 이동한 후 "서비스 맵 서버 그룹" 검색). 이러한 서버에 대한 서버 맵은 (구성된 동기화 간격에 따라) 다음 동기화와 동기화됩니다.

## <a name="monitoring-service-map"></a>서비스 맵 모니터링
OMS 작업 영역이 연결되면 SCOM 콘솔의 모니터링 창에 Service Map이라는 새 폴더가 표시됩니다.
![SCOM 모니터링](media/oms-service-map/scom-monitoring.png)

서비스 맵 폴더에는 3개의 노드가 있습니다.
### <a name="active-alerts"></a>활성 경고:
OMS에서 SCOM 및 서비스 맵 솔루션 간의 통신에 대한 모든 활성 경고를 표시합니다.

**참고:** SCOM에 표시되는 OMS 경고가 아닙니다.
### <a name="servers"></a>Servers:
서비스 맵에서 동기화하도록 구성된 모니터링 대상 서버의 목록이 들어 있습니다.

![SCOM 서버 모니터링](media/oms-service-map/scom-monitoring-servers.png)

### <a name="server-dependency-views"></a>서버 종속성 보기:
이 보기는 서비스 맵에서 동기화되는 모든 서버의 목록을 보여줍니다. 사용자가 원하는 서버를 클릭하면 해당 서버의 분산 응용 프로그램 다이어그램을 볼 수 있습니다.

![SCOM 분산 응용 프로그램 다이어그램](media/oms-service-map/scom-dad.png)

## <a name="editdelete-workspace"></a>작업 영역 편집/삭제:
사용자는 서비스 맵 개요 창(관리 창 --> Operations Management Suite --> 서비스 맵)을 통해 구성된 작업 영역을 편집하거나 삭제할 수 있습니다.  현재는 하나의 OMS 작업 영역만 구성할 수 있습니다.

![SCOM 작업 영역 편집](media/oms-service-map/scom-edit-workspace.png)

## <a name="configuring-rules-and-overrides"></a>규칙 및 재정의 구성:
**_Microsoft.SystemCenter.ServiceMapImport.Rule** 규칙은 서비스 맵에서 주기적으로 정보를 가져오기 위해 생성됩니다.  사용자는 이 규칙의 재정의를 구성하여 동기화 시간을 변경할 수 있습니다.
작성 창 --> 규칙 --> Microsoft.SystemCenter.ServiceMapImport.Rule

![SCOM 재정의](media/oms-service-map/scom-overrides.png)
* **Enabled** – 자동 업데이트 사용/사용 안 함 
* **IntervalSeconds** – 업데이트 간격.  기본값은 1시간입니다. 서버 맵을 더 자주 동기화하려면 여기에서 값을 변경할 수 있습니다.
* **TimeoutSeconds** – 요청 시간이 초과하기 전의 대기 시간 
* **TimeWindowMinutes** – 데이터 쿼리 간격.  기본값은 60분 간격입니다. 최대값은 1시간(서비스 맵이 허용하는 최대값)입니다.

## <a name="known-issueslimitations"></a>알려진 문제/제한 사항:
현재 디자인:
1. 사용자는 작성 창을 통해 수동으로 "서비스 맵 서버 그룹"에 서버를 추가할 수 있지만 이러한 서버에 대한 맵은 다음 동기화 주기 동안에만 서비스 맵에서 동기화됩니다(기본적으로 60분. 사용자가 동기화 시간을 재정의할 수 있습니다.). 
2. 사용자는 단일 OMS 작업 영역에 연결할 수 있습니다.

## <a name="creating-a-service-principal"></a>서비스 주체 만들기
다음 링크는 서비스 주체를 만드는 세 가지 방법에 대한 공식 Azure 설명서로 연결됩니다.
* [PowerShell에서 서비스 주체 만들기](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI에서 서비스 주체 만들기](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure Portal에서 서비스 주체 만들기](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>사용자 의견
서비스 맵 또는 이 설명서에 대한 의견이 있습니까?  기능을 제안하거나 기존 제안에 투표할 수 있는 [사용자 의견 페이지](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)를 방문하세요.

