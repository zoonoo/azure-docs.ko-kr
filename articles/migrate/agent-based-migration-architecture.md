---
title: Azure Migrate Server Migration의 에이전트 기반 마이그레이션
description: Azure Migrate의 에이전트 기반 VMware VM 마이그레이션에 대한 개요를 제공합니다.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: f4f79725d0eda65ba00a44e9e7fc2a51c024eccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864060"
---
# <a name="agent-based-migration-architecture"></a>에이전트 기반 마이그레이션 아키텍처

이 문서에서는 [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용하여 VMware VM의 에이전트 기반 복제에 사용되는 아키텍처와 프로세스에 대한 개요를 제공합니다.

Azure Migrate: Server Migration을 사용하면 다음과 같은 몇 가지 옵션을 통해 VMware VM을 복제할 수 있습니다.

- 이 문서에 설명된 대로 에이전트 기반 복제를 사용하여 VM을 마이그레이션합니다.
- 에이전트 없는 복제를 사용하여 VMware VM을 마이그레이션합니다. 이렇게 하면 VM에 아무것도 설치할 필요 없이 VM을 마이그레이션할 수 있습니다.

VMware VM에 대한 마이그레이션 방식 [선택 및 비교](server-migrate-overview.md)에 대해 자세히 알아보세요. 


## <a name="agent-based-migration"></a>에이전트 기반 마이그레이션

에이전트 기반 마이그레이션은 온-프레미스 VMware VM 및 물리적 서버를 Azure로 마이그레이션하는 데 사용됩니다. 또한 AWS 인스턴스 및 GCP VM을 포함하여 가상화된 다른 온-프레미스 서버뿐 아니라 프라이빗 및 퍼블릭 클라우드 VM을 마이그레이션하는 데도 사용할 수 있습니다. Azure Migrate의 에이전트 기반 마이그레이션은 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 서비스의 일부 백엔드 기능을 사용합니다.


## <a name="architectural-components"></a>아키텍처 구성 요소

이 다이어그램에서는 에이전트 기반 마이그레이션에 필요한 구성 요소를 보여 줍니다.

![다이어그램은 표에서 설명하는 에이전트 기반 마이그레이션의 구성 요소를 보여 줍니다.](./media/agent-based-replication-architecture/architecture.png)

이 표에는 에이전트 기반 마이그레이션에 사용되는 구성 요소가 요약되어 있습니다.

**구성 요소** | **세부 정보** | **설치**
--- | --- | ---
**복제 어플라이언스** | 복제 어플라이언스(구성 서버/프로세스 서버)는 온-프레미스 환경과 Server Migration 간의 다리 역할을 하는 온-프레미스 서버입니다. 어플라이언스는 온-프레미스 서버 인벤토리를 검색하여 Server Migration이 복제 및 마이그레이션을 오케스트레이션할 수 있도록 합니다. 어플라이언스에는 다음의 두 가지 구성 요소가 있습니다.<br/><br/> **구성 서버**: Server Migration에 연결하고 복제를 조정합니다.<br/> **프로세스 서버**: 데이터 복제를 처리합니다. 프로세스 서버는 서버 데이터를 수신하고 압축 및 암호화하여 Azure로 전송합니다. Azure에서 Server Migration은 관리 디스크에 데이터를 작성합니다. | 기본적으로 프로세스 서버는 복제 어플라이언스에 구성 서버와 함께 설치됩니다.
**모바일 서비스** | Mobility Service는 복제 및 마이그레이션할 각 서버에 설치되는 에이전트입니다. 서버에서 프로세스 서버로 복제 데이터를 전송합니다. | 여러 버전의 Mobility Service에 대한 설치 파일은 복제 어플라이언스에 있습니다. 운영 체제 및 복제하려는 서버 버전에 따라 필요한 에이전트를 다운로드하여 설치합니다.

## <a name="mobility-service-installation"></a>모바일 서비스 설치

다음 메서드를 사용하여 Mobility 서비스를 설치할 수 있습니다.

- **설치 푸시**: 서버에 대한 보호를 사용 설정 시 프로세스 서버가 Mobility Service를 설치합니다. 
- **수동으로 설치**: UI 또는 명령 프롬프트를 통해 각 서버에 Mobility Service를 수동으로 설치할 수 있습니다.

Mobility Service는 복제 어플라이언스 및 복제된 서버와 통신합니다. 복제 어플라이언스, 프로세스 서버 또는 복제되는 서버에서 바이러스 백신 소프트웨어가 실행되고 있는 경우 검색에서 다음 폴더를 제외해야 합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent(Mobility Service가 설치된 Windows 서버)

## <a name="replication-process"></a>복제 프로세스

1. 서버에 복제를 사용 설정하면 Azure에 대한 초기 복제가 시작됩니다.
2. 초기 복제 시 Mobility Service는 서버 디스크에서 데이터를 읽어와 프로세스 서버에 전송합니다.
3. 이 데이터는 Azure 구독에서 디스크의 복사본을 시드하는 데 사용됩니다. 
4. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 복제는 블록 수준이며 거의 연속으로 복제됩니다.
4. Mobility Service는 운영 체제의 스토리지 하위 시스템에 통합되어 디스크 메모리에 대한 쓰기 작업을 가로챕니다. 이 방식을 사용하면 증분 복제를 위해 복제 서버에서 디스크 I/O 작업이 방지됩니다. 
5. 추적된 서버 변경 내용은 인바운드 포트 HTTPS 9443에서 프로세스 서버로 전송됩니다. 이 포트는 수정할 수 있습니다. 프로세스 서버는 이 데이터를 압축 및 암호화하여 Azure로 전송합니다. 

## <a name="ports"></a>포트

**디바이스** | **연결**
--- | --- 
**서버 복제 중** | VM에서 실행되는 Mobility Service는 복제 관리를 위해 HTTPS 443 인바운드 포트의 온-프레미스 복제 어플라이언스와 통신합니다.<br/><br/> 서버는 복제 데이터를 HTTPS 9443 인바운드 포트에서 프로세스 서버로 보냅니다. 이 포트는 수정할 수 있습니다.
**복제 어플라이언스** | 복제 어플라이언스는 HTTPS 443 아웃바운드 포트를 통해 Azure를 사용하는 복제를 오케스트레이션합니다.
**프로세스 서버** | 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.


## <a name="performance-and-scaling"></a>성능 및 크기 조정

기본적으로 구성 서버와 프로세스 서버를 둘 다 실행하는 단일 복제 어플라이언스를 배포합니다. 서버를 몇 개만 복제하는 경우 이 배포로 충분합니다. 그러나 수백 대의 서버를 복제하고 마이그레이션하는 경우에는 단일 프로세스 서버에서 모든 복제 트래픽을 처리할 수 없습니다. 이 경우 추가적인 스케일 아웃 프로세스 서버를 배포할 수 있습니다.

### <a name="plan-vmware-deployment"></a>VMware 배포 계획

VMware VM을 복제하는 경우 [VMware에 대한 Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md)를 사용하여 일별 데이터 변화율 및 필요한 프로세스 서버를 비롯한 성능 요구 사항을 확인할 수 있습니다.

### <a name="replication-appliance-capacity"></a>복제 어플라이언스 용량

이 표의 값을 사용하여 배포에 추가적인 프로세스 서버가 필요한지를 확인할 수 있습니다.

- 일일 변화율(변동률)이 2TB를 초과하는 경우에는 추가적인 프로세스 서버를 배포합니다.
- 200개가 넘는 서버를 복제하는 경우에는 추가적인 복제 어플라이언스를 배포합니다.

**CPU** | **메모리** | **사용 가능한 공간-데이터 캐싱** | **변동률** | **복제 한도**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz) | 16GB | 300GB | 500GB 이하 | 서버 100개 미만 
12개 vCPU(2개 소켓 * 6코어 \@ 2.5GHz) | 18GB | 600GB | 501GB~1TB | 서버 100~150개
16개 vCPU(2개 소켓 * 8코어 \@ 2.5GHz) | 32GB |  1TB | 1TB ~ 2TB | 서버 151~200개

### <a name="sizing-scale-out-process-servers"></a>스케일 아웃 프로세스 서버 크기 조정

스케일 아웃 프로세스 서버를 배포해야 하는 경우 이 표를 사용하여 서버 크기 조정을 파악합니다.

**프로세스 서버** | **데이터 캐싱에 사용 가능한 공간** | **변동률** | **복제 한도**
--- | --- | --- | --- 
4개 vCPU(2개 소켓 * 2코어 \@ 2.5GHz) 8GB 메모리 | 300GB | 250GB 이하 | 서버 최대 85개 
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz), 12GB 메모리 | 600GB | 251GB~1TB | 서버 86~150개
vCPU 12개(소켓 2개 * 코어 6개 \@ 2.5GHz), 24GB 메모리 | 1TB | 1-2TB | 서버 151~225개

## <a name="throttle-upload-bandwidth"></a>업로드 대역폭을 제한합니다.

Azure에 복제하는 VMware 트래픽이 특정 프로세스 서버를 통과합니다. 프로세스 서버로 실행되는 서버에서 대역폭을 제한하여 업로드 처리량을 제한할 수 있습니다. 다음 레지스트리 키를 사용하여 대역폭을 조정할 수 있습니다.

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 레지스트리 값은 디스크의 데이터 전송(초기 또는 델타 복제)에 사용되는 스레드 수를 지정합니다. 값이 높을수록 복제에 사용되는 네트워크 대역폭이 증가합니다. 기본값은 4입니다. 최댓값은 32입니다. 트래픽을 모니터링하여 값을 최적화합니다.
- 또한 다음과 같이 프로세스 서버에서 대역폭을 제한할 수 있습니다.

    1. 프로세스 서버에서 Azure Backup MMC 스냅인을 엽니다. 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin 폴더에 바로 가기가 있습니다. 
    2. 스냅인에서 **속성 변경** 을 선택합니다.
    3. **대역폭 제한** 에서 **백업 작업에 인터넷 대역폭 사용량 제한 사용** 을 선택합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 512Kbps~1,023Mbps입니다.


## <a name="next-steps"></a>다음 단계

[VMware](tutorial-migrate-vmware-agent.md) 또는 [물리적 서버](tutorial-migrate-physical-virtual-machines.md)에 [에이전트 기반 마이그레이션](tutorial-migrate-vmware-agent.md)을 사용해 보세요.
