---
title: Azure Migrate Server 마이그레이션의 에이전트 기반 마이그레이션 아키텍처
description: Azure Migrate Server Migration을 사용 하 여 에이전트 기반 VMware VM 마이그레이션에 대 한 개요를 제공 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: f5ad3aa0fc51f47942750d3745ffef1d6e4a087d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232588"
---
# <a name="agent-based-migration-architecture"></a>에이전트 기반 마이그레이션 아키텍처

이 문서에서는 Azure Migrate 서버 마이그레이션 도구를 사용 하 여 에이전트 기반 복제에 사용 되는 아키텍처와 프로세스에 대 한 개요를 제공 합니다.

[Azure Migrate](migrate-services-overview.md) 은 온-프레미스 앱 및 워크 로드, Azure에 대 한 AWS/GCP VM 인스턴스의 검색, 평가 및 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

## <a name="agent-based-replication"></a>에이전트 기반 복제

Azure Migrate 서버 복제 도구의 에이전트 기반 복제는 온-프레미스 VMware Vm 및 물리적 서버를 Azure로 마이그레이션하는 데 사용 됩니다. 또한 AWS 인스턴스 및 GCP Vm을 포함 하 여 다른 온-프레미스 가상화 된 서버 뿐만 아니라 사설 및 공용 클라우드 Vm을 마이그레이션하는 데 사용할 수 있습니다.

VMware 마이그레이션의 경우 Azure Migrate 서버 마이그레이션 도구는 다음과 같은 몇 가지 옵션을 제공 합니다.

- 이 문서에 설명 된 대로 에이전트 기반 복제를 사용 하 여 마이그레이션
- 에이전트 없는 복제를 사용 하면 Vm을 설치할 필요 없이 Vm을 마이그레이션할 수 있습니다.

[VMware에 대 한 마이그레이션 방법 선택](server-migrate-overview.md)에 대해 자세히 알아보세요.

## <a name="server-migration-and-azure-site-recovery"></a>서버 마이그레이션 및 Azure Site Recovery

Azure Migrate Server 마이그레이션은 온-프레미스 및 공용 클라우드 워크 로드를 Azure로 마이그레이션하기 위한 도구입니다. 마이그레이션에 최적화 되어 있습니다. Site Recovery은 재해 복구 도구입니다. Azure 서버 마이그레이션과 Site Recovery는 데이터 복제에 사용 되는 몇 가지 일반적인 기술 구성 요소를 공유 하지만 다른 용도로 사용 됩니다.

## <a name="architectural-components"></a>아키텍처 구성 요소

![아키텍처](./media/agent-based-replication-architecture/architecture.png)

이 표에는 에이전트 기반 마이그레이션에 사용 되는 구성 요소가 요약 되어 있습니다.

**구성 요소** | **세부 정보** | **설치**
--- | --- | ---
**복제 어플라이언스** | 복제 어플라이언스 (구성 서버)는 온-프레미스 환경과 Azure Migrate 서버 마이그레이션 도구 간의 브리지 역할을 하는 온-프레미스 컴퓨터입니다. 어플라이언스는 온-프레미스 VM 인벤토리를 검색 하 여 Azure 서버 마이그레이션이 복제 및 마이그레이션을 오케스트레이션 할 수 있도록 합니다. 어플라이언스에는 다음 두 가지 구성 요소가 있습니다.<br/><br/> **구성 서버**: Azure Migrate 서버 마이그레이션에 연결 하 고 복제를 조정 합니다.<br/> **프로세스 서버**: 데이터 복제를 처리 합니다. VM 데이터를 수신 하 고, 압축 및 암호화 하 고, Azure 구독으로 보냅니다. 여기에서 서버 마이그레이션은 관리 디스크에 데이터를 기록 합니다. | 기본적으로 프로세스 서버는 복제 어플라이언스의 구성 서버와 함께 설치 됩니다.
**모바일 서비스** | 모바일 서비스는 복제 및 마이그레이션할 각 컴퓨터에 설치 되는 에이전트입니다. 컴퓨터의 복제 데이터를 프로세스 서버로 보냅니다. 다양 한 모바일 서비스 에이전트를 사용할 수 있습니다. | 모바일 서비스에 대 한 설치 파일은 복제 어플라이언스에 있습니다. 복제 하려는 컴퓨터의 운영 체제 및 버전에 따라 필요한 에이전트를 다운로드 하 여 설치 합니다.

### <a name="mobility-service-installation"></a>모바일 서비스 설치

다음 메서드를 사용하여 Mobility 서비스를 설치할 수 있습니다.

- **푸시 설치**: 컴퓨터에 대 한 보호를 사용 하도록 설정 하면 프로세스 서버에서 모바일 서비스를 설치 합니다. 
- **수동으로 설치**: UI 또는 명령 프롬프트를 통해 각 컴퓨터에 모바일 서비스를 수동으로 설치할 수 있습니다.

모바일 서비스는 복제 어플라이언스 및 복제 된 컴퓨터와 통신 합니다. 복제 어플라이언스, 프로세스 서버 또는 복제 중인 컴퓨터에서 바이러스 백신 소프트웨어가 실행 되 고 있는 경우 검색에서 다음 폴더를 제외 해야 합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (모바일 서비스가 설치 된 Windows 컴퓨터에서)

## <a name="replication-process"></a>복제 프로세스

1. VM에 대 한 복제를 사용 하도록 설정 하면 Azure에 대 한 초기 복제가 시작 됩니다.
2. 초기 복제 중에 모바일 서비스는 컴퓨터 디스크에서 데이터를 읽고 프로세스 서버에 보냅니다.
3. 이 데이터는 Azure 구독에서 디스크의 복사본을 시드 하는 데 사용 됩니다. 
4. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 복제는 블록 수준 이며 거의 연속으로 복제 됩니다.
4. 모바일 서비스는 운영 체제의 저장소 하위 시스템에 통합 하 여 VM 디스크 메모리에 대 한 쓰기를 차단 합니다. 이 메서드는 증분 복제를 위해 복제 컴퓨터에서 디스크 i/o 작업을 방지 합니다. 
5. 컴퓨터에 대 한 추적 된 변경 내용은 HTTPS 9443 인바운드 포트에서 프로세스 서버로 전송 됩니다. 이 포트는 수정할 수 있습니다. 프로세스 서버는 압축 및 암호화 하 고 Azure로 보냅니다. 

## <a name="ports"></a>포트

**장치** | **연결**
--- | --- 
VM | Vm에서 실행 되는 모바일 서비스는 복제 관리를 위해 HTTPS 443 인바운드 포트에서 온-프레미스 복제 어플라이언스와 통신 합니다.<br/><br/> Vm은 복제 데이터를 HTTPS 9443 인바운드 포트에서 프로세스 서버 (기본적으로 복제 어플라이언스에서 실행)로 전송 합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure를 사용 하 여 복제를 오케스트레이션 합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.


## <a name="performance-and-scaling"></a>성능 및 크기 조정

기본적으로 구성 서버와 프로세스 서버를 실행 하는 단일 복제 어플라이언스를 배포 합니다. 몇 대의 컴퓨터만 복제 하는 경우이 배포로 충분 합니다. 그러나 수백 대의 컴퓨터를 복제 하 고 마이그레이션하는 경우에는 단일 프로세스 서버에서 모든 복제 트래픽을 처리할 수 없습니다. 이 경우 추가 스케일 아웃 프로세스 서버를 배포할 수 있습니다.

### <a name="site-recovery-deployment-planner-for-vmware"></a>VMware에 대 한 Site Recovery Deployment Planner

VMware Vm을 복제 하는 경우 VMware에 대 한 [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) 를 사용 하 여 일별 데이터 변경 률 및 필요한 프로세스 서버를 비롯 한 성능 요구 사항을 확인할 수 있습니다.

### <a name="replication-appliance-capacity"></a>복제 어플라이언스 용량

이 테이블의 값을 사용 하 여 배포에 추가 프로세스 서버가 필요한 지 여부를 파악할 수 있습니다.

- 일일 변경 률 (변동 율)이 2tb를 초과 하는 경우 추가 프로세스 서버를 배포 합니다.
- 200 개 이상의 컴퓨터를 복제 하는 경우 추가 복제 어플라이언스를 배포 합니다.

**CPU** | **메모리** | **데이터 캐싱에 사용 가능한 공간** | **변동 율** | **복제 제한**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz) | 16GB | 300GB | 500GB 이하 | 컴퓨터 100대 미만 
12개 vCPU(2개 소켓 * 6코어 \@ 2.5GHz) | 18GB | 600GB | 501GB~1TB | 100-150 컴퓨터.
16개 vCPU(2개 소켓 * 8코어 \@ 2.5GHz) | 32 G1 |  1TB | 1TB ~ 2TB | 151-200 컴퓨터.

### <a name="scale-out-process-server-sizing"></a>스케일 아웃 프로세스 서버 크기 조정

스케일 아웃 프로세스 서버를 배포 해야 하는 경우이 표를 참조 하 여 서버 크기를 파악할 수 있습니다.

**프로세스 서버** | **데이터 캐싱에 사용 가능한 공간** | **변동 율** | **복제 제한**
--- | --- | --- | --- 
4개 vCPU(2개 소켓 * 2코어 \@ 2.5GHz), 8GB 메모리 | 300GB | 250GB 이하 | 최대 85 대의 컴퓨터 
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz), 12GB 메모리 | 600GB | 251GB~1TB    | 86-150 컴퓨터.
12 개 vcpus (2 개 소켓 * \@ 6 코어 2.5 g h z), 24gb 메모리 | 1TB | 1-2TB | 151-225 컴퓨터.

## <a name="control-upload-throughput"></a>업로드 처리량 제어


 Azure에 복제하는 VMware 트래픽이 특정 프로세스 서버를 통과합니다. 프로세스 서버로 실행 되는 컴퓨터에서 대역폭을 조정 하 여 업로드 처리량을 제한할 수 있습니다. 다음 레지스트리 키를 사용 하 여 대역폭에 영향을 줄 수 있습니다.

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 레지스트리 값은 디스크의 데이터 전송 (초기 또는 델타 복제)에 사용 되는 스레드 수를 지정 합니다. 값이 높을수록 복제에 사용되는 네트워크 대역폭이 증가합니다. 기본값은 4입니다. 최대값은 32입니다. 트래픽을 모니터링하여 값을 최적화합니다.
- 또한 다음과 같이 프로세스 서버 컴퓨터에서 대역폭을 제한할 수 있습니다.

    1. 프로세스 서버 컴퓨터에서 Azure Backup MMC 스냅인을 엽니다. 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 폴더에 바로 가기가 있습니다. 
    2. 스냅인에서 **속성 변경**을 선택합니다.
    3. **제한**에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택 합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 512Kbps~1,023Mbps입니다.


## <a name="next-steps"></a>다음 단계

Azure Migrate 서버 마이그레이션을 사용 하 여 에이전트 기반 [VMWARE VM 마이그레이션을](tutorial-migrate-vmware-agent.md) 사용해 보세요.
