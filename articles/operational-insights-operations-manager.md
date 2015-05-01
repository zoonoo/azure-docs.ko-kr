<properties 
   pageTitle="Operational Insights와 함께 Operations Manager 사용 시 고려 사항"
   description="Operations Manager와 함께 Microsoft Azure Operational Insights를 사용하는 경우, 구성은 Operations Manager 에이전트와 관리 그룹의 분포에 따라 분석을 위해 데이터를 Operational Insights 서비스로 보냅니다."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Operational Insights와 함께 Operations Manager 사용 시 고려 사항

Operations Manager와 함께 Microsoft Azure Operational Insights를 사용하는 경우, 구성은 Operations Manager 에이전트와 관리 그룹의 분포에 따라 분석을 위해 데이터를 Operational Insights 서비스로 보냅니다. 그러나 웹 서비스에 직접 연결하는 에이전트를 사용하는 경우 Operations Manager가 필요하지 않습니다.  Operations Manager와 함께 Operational Insights를 사용할 때는 다음 문제를 고려합니다.

## Operational Insights 소프트웨어 기능 및 요구 사항

Operational Insights는 클라우드의 웹 서비스 또는 사용자 환경에서 컴퓨터를 관리하는 Operations Manager 관리 그룹 및 Operations Manager 에이전트에 직접 연결하는 에이전트 중 하나로 구성됩니다.

Operations Manager 에이전트(데이터 수집) 및 관리 그룹(에이전트 관리 및 Operational Insights 서비스로 데이터 전송)을 선택하기 전에 다음을 이해하고 있어야 합니다.

- Operations Manager 에이전트는 데이터를 수집하고 분석하려는 모든 서버에 설치됩니다.

- Operations Manager 관리 그룹은 에이전트에서 Operational Insights 웹 서비스로 데이터를 전송합니다. 데이터를 분석하지 않습니다.

- Operations Manager 관리 그룹이 웹 서비스에 데이터를 업로드하려면 인터넷에 액세스할 수 있어야 합니다.

- 최상의 결과를 위해 도메인 컨트롤러도 되는 컴퓨터에 Operations Manager 관리 서버를 설치하지 마십시오.

- Operations Manager 에이전트는 데이터를 전송할 수 있도록 Operations Manager 관리 그룹이 네트워크에 연결되어 있어야 합니다.

Operational Insights는 System Center 상태 관리 서비스를 사용하여 데이터를 수집하고 분석할 수 있습니다. Operations Manager는 System Center 상태 서비스에 따라 달라집니다. 서버에 설치된 프로그램을 보는 경우, 특히 프로그램 추가/제거에서 System Center Operations Manager 에이전트 소프트웨어가 표시 됩니다. Operational Insights는 이 소프트웨어에 의존하므로 제거하지 마십시오  Operations Manager 에이전트 소프트웨어를 제거하면 Operational Insights가 더 이상 작동하지 않습니다.

## Operations Manager와의 공존

Operations Manager를 사용하는 경우 System Center Operations Manager 2012 R2 또는 System Center Operations Manager 2012 SP1에서 Operations Manager 에이전트와 Operational Insights만 지원됩니다. 이전 버전의 System Center Operations Manager에서는 지원되지 않습니다. Operations Manager 에이전트를 사용하여 데이터를 수집하기 때문에 SharePoint 2012와 같은 분석된 작업 부하의 일부를 지원하기 위해 특정 자격 증명(작업 계정 또는 실행 계정)을 사용합니다.

## Operational Insights 및 SQL Server 2012

Operations Manager를 사용하는 경우 System Center 상태 서비스는 로컬 시스템 계정으로 실행됩니다. SQL Server 2008 R2 이전 SQL Server 버전에서는 기본적으로 로컬 시스템 계정이 사용되며 시스템 관리자 서버 역할의 멤버입니다. SQL Server 2012에서 로컬 시스템 로그인은 시스템 관리자 서버 역할의 일부가 아닙니다. 결과적으로 Operational Insights를 사용하면, SQL Server 2012 인스턴스를 완전히 모니터링할 수 없으며 모든 규칙에서 경고를 생성할 수 있는 것은 아닙니다.

## 인터넷 및 내부 네트워크 연결

웹 서비스를 사용하여 직접 연결하는 에이전트를 사용하는 경우, 에이전트에서 웹 서비스로 데이터를 전송하고 웹 서비스에서 업데이트된 구성 정보를 수신하려면 인터넷에 액세스해야 합니다.

Operations Manager를 사용하여 관리 서버에서 웹 서비스로 데이터를 전송하고 웹 서비스에서 업데이트된 구성 정보를 수신하려면 인터넷에 액세스해야 합니다. 그러나 에이전트가 인터넷에 액세스되어 있지 않아도 됩니다. 인터넷에 연결되지 않은 서버에 Operations Manager가 있는 경우, 인터넷에 연결된 관리 서버와 통신할 수 있다면 웹 서비스를 사용할 수 있습니다.

## 클러스터링 지원

Operations Manager 에이전트는 Windows 장애 조치 클러스터의 일부가 되도록 구성된 Windows Server 2012, Windows Server 2008 R2 또는 Windows Server 2008을 실행하는 컴퓨터에서 지원됩니다. Operational Insights 포털에서 클러스터를 볼 수 있습니다. 서버 페이지에서 클러스터는 TYPE=CLUSTER로 식별됩니다(물리적 컴퓨터가 식별되는 방식인 TYPE=AGENT와 반대).

검색 및 구성 규칙은 클러스터의 활성 노드와 수동 노드에서 실행하지만 패시브 노드에서 생성 되는 모든 경고는 무시됩니다. 수동에서 활성으로 노드를 이동하면, 해당 노드에 대한 경고와 필요한 사용자의 개입 없이 자동으로 표시됩니다.

일부 경고는 경고를 생성하는 규칙에 따라 두 번 생성될 수 있습니다. 운영 체제를 검사하여 잘못된 드라이버를 감지하는 규칙은 물리적 서버 및 클러스터에 대한 경고를 생성합니다.

수동 노드의 구성 분석은 지원되지 않습니다.

Operational Insights는 동일한 장애 조치 클러스터의 일부인 Windows Server를 실행하는 컴퓨터의 연결 또는 그룹화를 지원하지 않습니다.

## Operational Insights 환경의 크기 조정

Operational Insights 배포를 계획할 때(특히 단일 관리 그룹을 통해 데이터를 전송하려는 Operations Manager 에이전트의 개수를 분석할 때), 파일 공간 측면에서 해당 서버의 용량을 고려합니다.

다음 변수를 고려합니다.

- 관리 그룹당 에이전트의 개수

- 에이전트에서 하루에 관리 그룹에 전송되는 데이터의 평균 크기. 기본적으로 각 에이전트는 하루 2번 관리 그룹에 CAB 파일을 업로드합니다. CAB 파일의 크기(예: SQL Server 엔진 및 숫자 데이터베이스의 수)는 서버 구성과 서버(예: 생성된 경고의 수) 및 서버의 상태에 따라 달라 집니다. 일반적으로 대부분의 경우 일별 업로드 크기는 100 KB보다 작습니다.

- 관리 그룹에 데이터를 유지하는 보관 기간(기본값은 5 일)

한 예로, 에이전트 및 기본 보관 기간 당 100 KB의 일일 업로드 크기를 가정하는 경우 관리 그룹에 대해 다음 저장소가 필요합니다.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>에이전트의 개수</b></td>
		<td><b>관리 그룹에 필요한 예상 공간</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2.5 MB(5개의 에이전트 x 100 KB 데이터/일 x 5일 = 2,500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50개의 에이전트 x 100 KB 데이터/일 x 5일 = 25,000 KB)</td>
    </tr>

    </tbody>
    </table>

## 지리적 위치

다양한 지리적 위치에 있는 서버에서 데이터를 분석하려는 경우 위치 당 하나의 관리 그룹이 있는 것이 좋습니다. 에이전트에서 관리 그룹으로 데이터 전송의 성능을 향상시킬 수 있습니다.



<!--HONumber=52-->