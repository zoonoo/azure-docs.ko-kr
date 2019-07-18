---
title: Azure 마이그레이션 서버 마이그레이션 마이그레이션 에이전트 기반 아키텍처
description: Azure 마이그레이션 서버 마이그레이션을 사용 하 여 에이전트 기반 VMware VM 마이그레이션에 간략하게 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811155"
---
# <a name="agent-based-migration-architecture"></a>에이전트 기반 마이그레이션 아키텍처

이 문서에서는 아키텍처와 Azure 마이그레이션 Server 마이그레이션 도구를 사용 하 여 에이전트 기반 복제에 사용 되는 프로세스의 개요를 제공 합니다.

[Azure Migrate](migrate-services-overview.md) 검색, 평가 및 마이그레이션에 온-프레미스 앱 및 워크 로드를 Azure에 AWS/GCP VM 인스턴스를 추적 하는 중앙 허브를 제공 합니다. 허브는 평가 및 마이그레이션 뿐만 아니라 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 대 한 Azure Migrate 도구를 제공합니다.

## <a name="agent-based-replication"></a>에이전트 기반 복제

Azure 마이그레이션 서버 복제에 마이그레이션 도구를 사용 하는 복제 에이전트 기반 온-프레미스 VMware Vm 및 물리적 서버. 사설 및 공용 클라우드 Vm에 AWS 인스턴스 등 GCP Vm 뿐만 아니라 다른 온-프레미스 가상화 서버를 마이그레이션하려면 데도 수 있습니다.

VMware 마이그레이션에 대 한 Azure 마이그레이션 Server 마이그레이션 도구는 두 가지 옵션을 제공합니다.

- 이 문서에 설명 된 대로 에이전트 기반 복제를 사용 하 여 마이그레이션.
- 에 아무 것도 설치 하지 않고도 Vm을 마이그레이션할 에이전트 없는 복제 합니다.

에 대해 자세히 알아보세요 [VMware에 대 한 마이그레이션 방법 선택](server-migrate-overview.md)합니다.

## <a name="server-migration-and-azure-site-recovery"></a>서버 마이그레이션 및 Azure Site Recovery

Azure 마이그레이션 서버 마이그레이션 마이그레이션 온-프레미스 및 공용 클라우드 워크 로드를 Azure에 대 한 도구 이며 마이그레이션에 대 한 최적화 되었습니다. Site Recovery는 재해 복구 도구입니다. Azure 서버 마이그레이션 및 Site Recovery에는 데이터 복제에 사용 되는 몇 가지 일반적인 기술 구성 요소를 공유 하지만 다른 용도로 사용 합니다.

## <a name="architectural-components"></a>아키텍처 구성 요소

![아키텍처](./media/agent-based-replication-architecture/architecture.png)

표에서 에이전트 기반 마이그레이션에 사용 되는 구성 요소입니다.

**구성 요소** | **세부 정보** | **설치**
--- | --- | ---
**복제 어플라이언스** | 복제 어플라이언스 (구성 서버)는 온-프레미스 컴퓨터는 온-프레미스 환경과 Azure 마이그레이션 Server 마이그레이션 도구 간의 다리 역할입니다. 어플라이언스는 Azure 서버 마이그레이션 복제 및 마이그레이션 오케스트레이션 수 있도록 온-프레미스 VM 인벤토리를 검색 합니다. 어플라이언스의 두 구성 요소:<br/><br/> **구성 서버**: Azure 마이그레이션 서버 마이그레이션에 연결 하 고 복제를 조정 합니다.<br/> **프로세스 서버**: 데이터 복제를 처리합니다. VM 데이터를 압축 및 암호화 하며, 받고 Azure 구독에 보냅니다. 여기서 서버 마이그레이션 managed disks로 데이터를 씁니다. | 기본적으로 프로세스 서버는 복제 어플라이언스에서 구성 서버와 함께 설치 됩니다.
**모바일 서비스** | 모바일 서비스의 마이그레이션하고 복제 하려는 각 컴퓨터에 에이전트를 설치 합니다. 컴퓨터에서 프로세스 서버로 복제 데이터를 보냅니다. 사용 가능한 다른 모바일 서비스 에이전트의 여러 가지가 있습니다. | 모바일 서비스에 대 한 설치 파일은 복제 어플라이언스에 있습니다. 다운로드 하 고 운영 체제 및 복제 하려는 컴퓨터의 버전에 따라 필요한 에이전트를 설치 합니다.

### <a name="mobility-service-installation"></a>모바일 서비스 설치

다음 메서드를 사용하여 Mobility 서비스를 설치할 수 있습니다.

- **푸시 설치**: 컴퓨터에 대 한 보호를 사용 하도록 설정 하면 모바일 서비스가 프로세스 서버에서 설치 됩니다. 
- **수동으로 설치**: UI 또는 명령 프롬프트를 통해 각 컴퓨터에 모바일 서비스를 수동으로 설치할 수 있습니다.

복제 어플라이언스와 통신 하 고 컴퓨터를 복제 하는 모바일 서비스. 복제 어플라이언스, 프로세스 서버 또는 복제 중인 컴퓨터에서 실행 중인 바이러스 백신 소프트웨어를 사용 하는 경우 다음 폴더는 검색에서 제외 되어야 합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- (설치 하는 모바일 서비스를 사용 하 여 Windows 컴퓨터)에서 C:\ProgramData\ASR\agent

## <a name="replication-process"></a>복제 프로세스

1. VM의 복제를 사용 하도록 설정 하면 Azure에 초기 복제 시작 합니다.
2. 초기 복제 시 모바일 서비스 컴퓨터 디스크에서 데이터를 읽고 프로세스 서버로 보냅니다.
3. 이 데이터는 Azure 구독에서 디스크의 복사본을 시드하기 위해 사용 됩니다. 
4. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 복제는 블록 수준이 고 거의 연속적입니다.
4. 모바일 서비스 가로채 운영 체제의 저장소 하위 시스템을 사용 하 여 통합 하 여 VM 디스크 메모리를 씁니다. 이 메서드는 증분 복제에 대 한 복제 하는 컴퓨터의 디스크 I/O 작업을 방지합니다. 
5. 컴퓨터에 대해 추적 된 변경 내용은 인바운드 HTTPS 9443 포트에서 프로세스 서버로 전송 됩니다. 이 포트는 수정할 수 있습니다. 프로세스 서버 압축 및 암호화 하며, 하 고 Azure에 전송 합니다. 

## <a name="ports"></a>포트

**장치** | **연결**
--- | --- 
VM | Vm에서 실행 되는 모바일 서비스와 통신 온-프레미스 복제 어플라이언스 HTTPS 443 포트에서 인바운드 복제 관리에 대 한 합니다.<br/><br/> Vm 복제 데이터를 보내도록 프로세스 서버 (기본적으로 복제 어플라이언스에서 실행) HTTPS 9443 포트에 인바운드 합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure 사용 하 여 복제를 오케스트레이션합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.


## <a name="performance-and-scaling"></a>성능 및 크기 조정

기본적으로 구성 서버와 프로세스 서버를 실행 하는 단일 복제 어플라이언스를 배포할 수 있습니다. 몇 개의 컴퓨터를 복제만 하는 경우이 배포는 충분 합니다. 그러나 복제 수백 대의 컴퓨터로 마이그레이션하는 경우 단일 프로세스 서버 수는 모든 복제 트래픽을 처리할 수 없습니다. 이 경우 추가 하 고 스케일 아웃 프로세스 서버를 배포할 수 있습니다.

### <a name="site-recovery-deployment-planner-for-vmware"></a>VMware에 대 한 site Recovery Deployment Planner

VMware Vm을 복제 하는 경우 사용할 수 있습니다 합니다 [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) 성능 요구 사항을 확인 하는 데 VMware, 일일 데이터를 포함 하 여 변경 속도 및 프로세스 서버가 필요 합니다.

### <a name="replication-appliance-capacity"></a>복제 어플라이언스 용량

이 표에 있는 값에 추가 프로세스 서버를 배포에서 해야 하는지 여부를 사용할 수 있습니다.

- 2TB가 넘는 일일 변경 률 (이탈 률)을 사용 하는 경우 추가 프로세스 서버를 배포 합니다.
- 200 대 보다 많은 컴퓨터를 복제 하는 경우 추가 복제 어플라이언스를 배포 합니다.

**CPU** | **메모리** | **데이터 캐싱에 사용 가능한 공간** | **변동률** | **복제 제한**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz) | 16GB | 300GB | 500GB 이하 | 컴퓨터 100대 미만 
12개 vCPU(2개 소켓 * 6코어 \@ 2.5GHz) | 18GB | 600GB | 501GB~1TB | 100 ~ 150 대 컴퓨터입니다.
16개 vCPU(2개 소켓 * 8코어 \@ 2.5GHz) | 32 G1 |  1TB | 1TB ~ 2TB | 151 ~ 200 대 컴퓨터입니다.

### <a name="scale-out-process-server-sizing"></a>스케일 아웃 프로세스 서버 크기 지정

스케일 아웃 프로세스 서버를 배포 해야 할 경우 서버 크기를 파악할 수 있습니다이 테이블 도움이 됩니다.

**프로세스 서버** | **데이터 캐싱에 사용 가능한 공간** | **변동률** | **복제 제한**
--- | --- | --- | --- 
4개 vCPU(2개 소켓 * 2코어 \@ 2.5GHz), 8GB 메모리 | 300GB | 250GB 이하 | 최대 85 대의 컴퓨터 
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz), 12GB 메모리 | 600GB | 251GB~1TB    | 86 150 컴퓨터입니다.
12 개의 Vcpu (소켓 * 6 코어 \@ 2.5ghz), 24GB 메모리 | 1TB | 1-2TB | 151 225 컴퓨터입니다.

## <a name="control-upload-throughput"></a>컨트롤 업로드 처리량

각 Hyper-v 호스트에서 Azure로 데이터를 업로드 하는 데 사용 되는 대역폭 양을 제한할 수 있습니다. 조심하세요. 값을 너무 낮게 설정 하면 부정적인 복제에 영향을 줄 고 하면 마이그레이션이 지연 합니다.


1. Hyper-v 호스트 또는 클러스터 노드에 로그인 합니다.
2. 실행할 **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**를 Windows Azure Backup MMC 스냅인을 엽니다.
3. 스냅인에서 **속성 변경**을 선택합니다.
4. **제한**를 선택 **인터넷 대역폭 사용 제한 백업 작업에 대 한**합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 512Kbps~1,023Mbps입니다.
I

### <a name="influence-upload-efficiency"></a>영향 업로드 효율성

복제에 대 한 여분의 대역폭 있고 업로드를 늘려야 할 경우 다음과 같이 업로드 작업에 할당 된 스레드 수를 늘릴 수 있습니다.

1. Regedit를 사용 하 여 레지스트리를 엽니다.
2. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 키로 이동
3. 각 복제 하는 VM에 대 한 데이터 업로드에 사용 되는 스레드의 수에 대 한 값을 늘립니다. 기본값은 4 및 최 댓 값은 32입니다. 

## <a name="next-steps"></a>다음 단계

에이전트 기반 사용해 보세요 [VMware VM 마이그레이션](tutorial-migrate-vmware-agent.md) Azure 마이그레이션 서버 마이그레이션을 사용 하 여 합니다.
