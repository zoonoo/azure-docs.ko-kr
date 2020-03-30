---
title: Azure 마이그레이션 서버 마이그레이션의 에이전트 기반 마이그레이션
description: Azure 마이그레이션에서 에이전트 기반 VM웨어 VM 마이그레이션에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425863"
---
# <a name="agent-based-migration-architecture"></a>에이전트 기반 마이그레이션 아키텍처

이 문서에서는 [Azure 마이그레이션: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용하여 VMware VM의 에이전트 기반 복제에 사용되는 아키텍처 및 프로세스에 대한 개요를 제공합니다.

Azure 마이그레이션: 서버 마이그레이션을 사용하여 다음과 같은 몇 가지 옵션을 사용하여 VMware VM을 복제할 수 있습니다.

- 이 문서에서 설명한 대로 에이전트 기반 복제를 사용하여 VM을 마이그레이션합니다.
- 에이전트 없는 복제를 사용하여 VMware VM을 마이그레이션합니다. 이렇게 하면 VM에 아무 것도 설치할 필요 없이 VM을 마이그레이션할 수 있습니다.

VMware VM에 대한 마이그레이션 방법 [선택 및 비교에](server-migrate-overview.md) 대해 자세히 알아보십시오. 


## <a name="agent-based-migration"></a>에이전트 기반 마이그레이션

에이전트 기반 마이그레이션은 온-프레미스 VM및 물리적 서버를 Azure로 마이그레이션하는 데 사용됩니다. 또한 AWS 인스턴스 및 GCP VM을 포함한 프라이빗 및 퍼블릭 클라우드 VM뿐만 아니라 다른 온-프레미스 가상화된 서버를 마이그레이션하는 데도 사용할 수 있습니다. Azure 마이그레이션의 에이전트 기반 마이그레이션은 [Azure 사이트 복구](../site-recovery/site-recovery-overview.md) 서비스의 일부 백 엔드 기능을 사용합니다.


## <a name="architectural-components"></a>아키텍처 구성 요소

이 다이어그램은 에이전트 기반 마이그레이션과 관련된 구성 요소를 보여 줍니다.

![Architecture](./media/agent-based-replication-architecture/architecture.png)

이 표에는 에이전트 기반 마이그레이션에 사용되는 구성 요소가 요약되어 있습니다.

**구성 요소** | **세부 정보** | **설치**
--- | --- | ---
**복제 어플라이언스** | 복제 어플라이언스(구성 서버/프로세스 서버)는 온-프레미스 환경과 서버 마이그레이션 간의 브리지 역할을 하는 온-프레미스 컴퓨터입니다. 어플라이언스는 온-프레미스 시스템 인벤토리를 검색하여 서버 마이그레이션이 복제 및 마이그레이션을 오케스트레이션할 수 있도록 합니다. 어플라이언스에는 다음 두 가지 구성 요소가 있습니다.<br/><br/> **구성 서버**: 서버 마이그레이션에 연결하고 복제를 조정합니다.<br/> **프로세스 서버**: 데이터 복제를 처리합니다. 프로세스 서버는 컴퓨터 데이터를 수신하고 압축 및 암호화하여 Azure로 보냅니다. Azure에서 서버 마이그레이션은 관리되는 디스크에 데이터를 씁니다. | 기본적으로 프로세스 서버는 복제 어플라이언스의 구성 서버와 함께 설치됩니다.
**모빌리티 서비스** | 모빌리티 서비스는 복제하고 마이그레이션하려는 각 컴퓨터에 설치된 에이전트입니다. 컴퓨터에서 프로세스 서버로 복제 데이터를 보냅니다. | 다른 버전의 Mobility 서비스에 대한 설치 파일은 복제 어플라이언스에 있습니다. 복제하려는 컴퓨터의 운영 체제 및 버전에 따라 필요한 에이전트를 다운로드하여 설치합니다.

## <a name="mobility-service-installation"></a>모바일 서비스 설치

다음 메서드를 사용하여 Mobility 서비스를 설치할 수 있습니다.

- **푸시 설치**: 기계 보호를 활성화하면 프로세스 서버에서 이동성 서비스를 설치합니다. 
- **수동 설치**: UI 또는 명령 프롬프트를 통해 각 컴퓨터에 수동으로 Mobility 서비스를 설치할 수 있습니다.

모빌리티 서비스는 복제 어플라이언스 및 복제된 컴퓨터와 통신합니다. 복제 어플라이언스, 프로세스 서버 또는 컴퓨터가 복제되는 바이러스 백신 소프트웨어가 있는 경우 다음 폴더는 검색에서 제외해야 합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\에이전트(이동성 서비스가 설치된 Windows 컴퓨터에서)

## <a name="replication-process"></a>복제 프로세스

1. 컴퓨터에 대한 복제를 사용하도록 설정하면 Azure에 대한 초기 복제가 시작됩니다.
2. 초기 복제 중에 Mobility 서비스는 컴퓨터 디스크의 데이터를 읽고 프로세스 서버로 보냅니다.
3. 이 데이터는 Azure 구독에서 디스크의 복사본을 시드하는 데 사용됩니다. 
4. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 복제는 블록 수준이며 거의 연속적입니다.
4. Mobility 서비스는 운영 체제의 저장소 하위 시스템과 통합하여 디스크 메모리에 쓰기를 차단합니다. 이 메서드는 증분 복제를 위해 복제 컴퓨터에서 디스크 I/O 작업을 방지합니다. 
5. 컴퓨터에 대한 추적된 변경 내용은 인바운드 포트 HTTPS 9443의 프로세스 서버로 전송됩니다. 이 포트는 수정할 수 있습니다. 프로세스 서버는 압축하고 암호화하여 Azure로 보냅니다. 

## <a name="ports"></a>포트

**디바이스** | **연결**
--- | --- 
**복제 기계** | VM에서 실행되는 모빌리티 서비스는 복제 관리를 위해 포트 HTTPS 443 인바운드의 온-프레미스 복제 어플라이언스와 통신합니다.<br/><br/> 컴퓨터는 포트 HTTPS 9443 인바운드의 프로세스 서버로 복제 데이터를 보냅니다. 이 포트는 수정할 수 있습니다.
**복제 어플라이언스** | 복제 어플라이언스는 포트 HTTPS 443 아웃바운드를 통해 Azure를 통해 복제를 오케스트레이션합니다.
**프로세스 서버** | 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.


## <a name="performance-and-scaling"></a>성능 및 크기 조정

기본적으로 구성 서버와 프로세스 서버를 모두 실행하는 단일 복제 어플라이언스를 배포합니다. 몇 대의 컴퓨터만 복제하는 경우 이 배포로 충분합니다. 그러나 수백 대의 컴퓨터를 복제하고 마이그레이션하는 경우 단일 프로세스 서버가 모든 복제 트래픽을 처리하지 못할 수 있습니다. 이 경우 추가 확장 프로세스 서버를 배포할 수 있습니다.

### <a name="plan-vmware-deployment"></a>VMware 배포 계획

VMware VM을 복제하는 경우 [VMware용 사이트 복구 배포 플래너를](../site-recovery/site-recovery-deployment-planner.md)사용하여 일일 데이터 변경률 및 필요한 프로세스 서버를 비롯한 성능 요구 사항을 결정할 수 있습니다.

### <a name="replication-appliance-capacity"></a>복제 어플라이언스 용량

이 테이블의 값을 사용하여 배포에 추가 프로세스 서버가 필요한지 여부를 파악합니다.

- 일일 변경률(이탈률)이 2TB를 초과하면 추가 프로세스 서버를 배포합니다.
- 200대 이상의 컴퓨터를 복제하는 경우 추가 복제 어플라이언스를 배포합니다.

**Cpu** | **메모리** | **여유 공간 데이터 캐싱** | **이탈률** | **복제 제한**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz) | 16GB | 300GB | 500GB 이하 | 컴퓨터 100대 미만 
12개 vCPU(2개 소켓 * 6코어 \@ 2.5GHz) | 18GB | 600GB | 501GB~1TB | 100-150 기계.
16개 vCPU(2개 소켓 * 8코어 \@ 2.5GHz) | 32 G1 |  1TB | 1TB ~ 2TB | 151-200 기계.

### <a name="sizing-scale-out-process-servers"></a>스케일 아웃 프로세스 서버 크기 조정

확장 프로세스 서버를 배포해야 하는 경우 이 테이블을 사용하여 서버 크기를 파악합니다.

**프로세스 서버** | **데이터 캐싱을 위한 여유 공간** | **이탈률** | **복제 제한**
--- | --- | --- | --- 
4개 vCPU(2개 소켓 * 2코어 \@ 2.5GHz) 8GB 메모리 | 300GB | 250GB 이하 | 최대 85대의 기계 
8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz), 12GB 메모리 | 600GB | 251GB~1TB    | 86-150 기계.
12 vCPU(소켓 2개 * \@ 6코어 2.5GHz), 24GB 메모리 | 1TB | 1-2TB | 151-225 기계.

## <a name="throttle-upload-bandwidth"></a>스로틀 업로드 대역폭.

Azure에 복제하는 VMware 트래픽이 특정 프로세스 서버를 통과합니다. 프로세스 서버로 실행되는 컴퓨터에서 대역폭을 제한하여 업로드 처리량을 제한할 수 있습니다. 이 레지스트리 키를 사용하여 대역폭에 영향을 주면

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 레지스트리 값은 디스크의 데이터 전송(초기 또는 델타 복제)에 사용되는 스레드 수를 지정합니다. 값이 높을수록 복제에 사용되는 네트워크 대역폭이 증가합니다. 기본값은 4입니다. 최대값은 32입니다. 트래픽을 모니터링하여 값을 최적화합니다.
- 또한 다음과 같이 프로세스 서버 컴퓨터에서 대역폭을 제한할 수 있습니다.

    1. 프로세스 서버 컴퓨터에서 Azure 백업 MMC 스냅인을 엽니다. 바탕 화면 또는 폴더 C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\빈에 바로 가기를 있습니다. 
    2. 스냅인에서 **속성 변경**을 선택합니다.
    3. **제한에서** **백업 작업에 대한 인터넷 대역폭 사용 제한 을**선택합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 512Kbps~1,023Mbps입니다.


## <a name="next-steps"></a>다음 단계

[VMware](tutorial-migrate-vmware-agent.md) 또는 실제 서버에 대한 [에이전트 기반 마이그레이션을](tutorial-migrate-vmware-agent.md) 사용해 보십시오. [physical servers](tutorial-migrate-physical-virtual-machines.md)
