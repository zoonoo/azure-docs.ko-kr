---
title: Azure Migrate의 VMware 평가 지원
description: Azure Migrate의 VMware 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029012"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 평가를 위한 지원 매트릭스 

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-migration-tool)를 사용 하 여 VMware vm을 평가 하기 위한 지원 설정 및 제한 사항을 요약 합니다. VMware Vm을 Azure로 마이그레이션하는 방법에 대 한 정보를 찾고 있는 경우 [마이그레이션 지원 매트릭스](migrate-support-matrix-vmware-migration.md)를 검토 하세요.

## <a name="overview"></a>개요

이 문서를 사용 하 여 Azure로의 마이그레이션에 대 한 온-프레미스 컴퓨터를 평가 하려면 Azure Migrate: 서버 평가 도구를 Azure Migrate 프로젝트에 추가 합니다. [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포 합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색 하 고 구성 및 성능 데이터를 Azure로 전송 합니다. 컴퓨터 검색 후에는 검색 된 컴퓨터를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한**| 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 최대 35000 VMware vm을 검색 하 고 평가 합니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트는 VMware Vm, Hyper-v Vm 및 물리적 서버를 평가 제한까지 포함할 수 있습니다.
**검색** | Azure Migrate 어플라이언스는 vCenter Server에서 VMware Vm을 1만 개까지 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .


## <a name="application-discovery"></a>애플리케이션 검색

컴퓨터를 검색 하는 것 외에도 Azure Migrate: 서버 평가는 컴퓨터에서 실행 되는 앱, 역할 및 기능을 검색할 수 있습니다. 앱 인벤토리를 검색 하면 온-프레미스 워크 로드에 맞게 조정 된 마이그레이션 경로를 식별 하 고 계획할 수 있습니다. 

**지원** | **세부 정보**
--- | ---
검색 | 검색은 에이전트 없이 컴퓨터 게스트 자격 증명을 사용 하 고 WMI 및 SSH 호출을 사용 하 여 컴퓨터에 원격으로 액세스 합니다.
지원 되는 컴퓨터 | 온-프레미스 VMware Vm.
컴퓨터 운영 체제 | 모든 Windows 및 Linux 버전
자격 증명 | 현재에서는 모든 Windows server에 대해 하나의 자격 증명을 사용할 수 있으며 모든 Linux 서버에 대해 하나의 자격 증명을 사용할 수 있습니다.<br/><br/> Windows Vm에 대 한 게스트 사용자 계정과 모든 Linux Vm에 대 한 일반/일반 사용자 계정 (비 sudo 액세스)을 만듭니다.
제한 | 앱 검색의 경우 어플라이언스 당 최대 1만 개를 검색할 수 있습니다. 

## <a name="vmware-requirements"></a>VMware 요구 사항

**VMware** | **세부 정보**
--- | ---
**vCenter Server** | 검색 하 고 평가 하려는 컴퓨터는 vCenter Server 버전 5.5, 6.0, 6.5 또는 6.7에서 관리 해야 합니다.
**사용 권한 (평가)** | 읽기 전용 계정을 vCenter Server 합니다.
**권한 (앱 검색)** | 읽기 전용 액세스 권한이 있는 vCenter Server 계정 및 가상 컴퓨터에 대 한 권한이 설정 된 게스트 작업 > 합니다.
**권한 (종속성 시각화)** | 읽기 전용 액세스 권한이 있는 센터 서버 계정 및 **게스트 작업** > **가상 컴퓨터** 에 대 한 권한이 설정 되어 있습니다.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 사용 합니다. VMware에 대 한 어플라이언스는 OVA 템플릿을 사용 하 여 배포 되며 vCenter Server로 가져왔습니다. 

- VMware에 대 한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware) 에 대해 알아봅니다.
- 어플라이언스에서 액세스 해야 하는 [url](migrate-appliance.md#url-access) 에 대해 알아봅니다.

## <a name="port-access"></a>포트 액세스

**디바이스** | **연결**
--- | ---
어플라이언스 | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/><br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위한 포트 44368의 인바운드 연결: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443, 5671 및 5672의 아웃 바운드 연결
vCenter Server | 어플라이언스에서 평가를 위한 구성 및 성능 메타 데이터를 수집할 수 있도록 TCP 포트 443에서 인바운드 연결 <br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결 됩니다. VCenter 서버가 다른 포트에서 수신 대기 하는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.

## <a name="dependency-visualization"></a>종속성 시각화

종속성 시각화를 사용 하면 평가 하 고 마이그레이션하려는 컴퓨터 간에 종속성을 시각화할 수 있습니다. 신뢰 수준이 높은 컴퓨터를 평가 하려는 경우 일반적으로 종속성 매핑을 사용 합니다. VMware Vm의 경우 종속성 시각화는 다음과 같이 지원 됩니다.

- **에이전트 없는 종속성 시각화**:이 옵션은 현재 미리 보기 상태입니다. 컴퓨터에 에이전트를 설치 하지 않아도 됩니다.
    - 사용 하도록 설정 된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동 합니다. 종속성 검색을 시작한 후에는 기기가 5 분의 폴링 간격으로 컴퓨터에서 데이터를 수집 합니다.
    - 수집 되는 데이터는 다음과 같습니다.
        - TCP 연결
        - 활성 연결이 있는 프로세스의 이름
        - 위의 프로세스를 실행 하는 설치 된 응용 프로그램의 이름
        - 아닙니다. 모든 폴링 간격에서 감지 된 연결
- **에이전트 기반 종속성 시각화**: 에이전트 기반 종속성 시각화를 사용 하려면 분석 하려는 각 온-프레미스 컴퓨터에 다음 에이전트를 다운로드 하 여 설치 해야 합니다.
    - 각 컴퓨터에 MMA (Microsoft Monitoring agent)를 설치 합니다. MMA 에이전트를 설치 하는 방법에 [대해 자세히 알아보세요](how-to-create-group-machine-dependencies.md#install-the-mma) .
    - 각 컴퓨터에 종속성 에이전트를 설치 합니다. 종속성 에이전트를 설치 하는 방법에 [대해 자세히 알아보세요](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) .
    - 또한 인터넷에 연결되지 않은 머신이 있으면 해당 머신에 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.


## <a name="next-steps"></a>다음 단계

- 평가 만들기에 대 한 모범 사례를 [검토](best-practices-assessment.md) 합니다.
- [VMware 평가를 준비](tutorial-prepare-vmware.md) 합니다.
