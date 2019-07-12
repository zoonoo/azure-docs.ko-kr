---
title: Azure 마이그레이션 서버 마이그레이션을 사용 하 여 Hyper-v 마이그레이션은 어떻게 작동 하나요? | Microsoft Docs
description: Azure 마이그레이션 서버 마이그레이션에서 Hyper-v 마이그레이션 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811714"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-v 복제는 어떻게 작동 하나요?

이 문서에서는 아키텍처와 Azure 마이그레이션 Server 마이그레이션 도구를 사용 하 여 Hyper-v Vm을 마이그레이션할 때 사용 하는 프로세스의 개요를 제공 합니다.

[Azure Migrate](migrate-services-overview.md) 검색, 평가 및 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 Vm에서 Azure로 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 허브는 평가 및 마이그레이션 뿐만 아니라 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 대 한 Azure Migrate 도구를 제공합니다.

## <a name="agentless-migration"></a>에이전트 없는 마이그레이션

Azure 마이그레이션 Server 마이그레이션 도구는 Hyper-v에 대 한 최적화 된 마이그레이션 워크플로 사용 하 여 온-프레미스 Hyper-v Vm에 대 한 에이전트 없는 복제를 제공 합니다. Hyper-v 호스트 또는 클러스터 노드의 에서만 소프트웨어 에이전트를 설치 합니다. Hyper-v Vm에 설치 해야 하는 아무 작업도 수행 합니다.

## <a name="server-migration-and-azure-site-recovery"></a>서버 마이그레이션 및 Azure Site Recovery

Azure 마이그레이션 서버 마이그레이션은 온-프레미스 워크 로드 마이그레이션 및 클라우드 기반 Vm을 Azure에 대 한 도구입니다. Site Recovery는 재해 복구 도구입니다. 도구는 데이터 복제에 사용 되는 몇 가지 일반적인 기술 구성 요소를 공유 하지만 다른 용도로 사용 합니다. 


## <a name="architectural-components"></a>아키텍처 구성 요소

![아키텍처](./media/hyper-v-replication-architecture/architecture.png)



**구성 요소** | **배포** | 
--- | --- 
**복제 공급자** | Microsoft Azure Site Recovery 공급자는 Hyper-v 호스트에 설치 되며 Azure 마이그레이션 서버 마이그레이션을 사용 하 여 등록 합니다.<br/> 공급자는 Hyper-v Vm에 대 한 복제를 오케스트레이션합니다.
**Recovery Services 에이전트** | Microsoft Azure 복구 서비스 에이전트를 데이터 복제를 처리합니다. Azure로 Hyper-v Vm에서 데이터를 복제 하는 공급자를 사용 하 여 작동 합니다.<br/> 복제 된 데이터는 Azure 구독의 저장소 계정에 업로드 됩니다. 서버를 마이그레이션하는 복제 된 데이터를 프로세스 도구 및 구독에 대 한 복제본 디스크에 적용 됩니다. 복제본 디스크 마이그레이션하는 경우 Azure Vm을 만드는 데 사용 됩니다.

- 구성 요소는 포털에서 Azure 마이그레이션 서버 마이그레이션에서 다운로드 한 단일 설치 파일에서 설치 됩니다.
- 공급자 및 어플라이언스를 Azure 마이그레이션 서버 마이그레이션와 통신 하려면 HTTPS 포트 443 아웃 바운드 연결을 사용 합니다.
- 공급자 및 에이전트 로부터의 통신 안전 하 게 암호화 됩니다.


## <a name="replication-process"></a>복제 프로세스

1. Hyper-v VM의 복제를 사용 하도록 설정 하면 초기 복제가 시작 됩니다.
2. Hyper-v VM 스냅숏이 만들어집니다.
3. VM에서 Vhd 됩니다는 모두 Azure에 복사--하나씩 복제 합니다. 초기 복제 시간 VM 크기 및 네트워크 대역폭에 따라 달라 집니다.
4. Hyper-v 복제본을 사용 하 고 로그 파일 (hrl 파일)에 저장 된 초기 복제 하는 동안 발생 하는 디스크 변경 내용이 추적 됩니다.
    - 로그 파일은 디스크와 동일한 폴더에 있습니다.
    - 각 디스크에는 보조 저장소로 전송 되는 연결 된 hrl 파일을 있습니다.
    - 초기 복제 진행 중에는 스냅샷과 로그 파일이 디스크 리소스를 사용합니다.
4. 초기 복제 완료 되 면 VM 스냅숏이 삭제 되 고 델타 복제가 시작 됩니다.
5. Hrl 파일에서 증분 디스크 변경 내용이 추적 됩니다. 복제 로그를 주기적으로 Recovery Services 에이전트에서 Azure storage 계정에 업로드 됩니다.


## <a name="performance-and-scaling"></a>성능 및 크기 조정

Hyper-v에 대 한 복제 성능 Azure에서 VM 크기, Vm의 데이터 변경 률 (변동), 로그 파일 저장소에 복제 데이터 업로드 대역폭 및 대상 저장소에 대 한 Hyper-v 호스트에서 사용 가능한 공간을 포함 하는 요인이 영향을 받습니다.

- 동시에 여러 컴퓨터를 복제 하는 경우 사용 합니다 [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) Hyper-v 복제를 최적화할 수 있도록에 대 한 합니다.
- Hyper-v 복제를 계획 하 고 복제를 Azure storage 계정 용량에 따라 분산 키를 누릅니다.

### <a name="control-upload-throughput"></a>컨트롤 업로드 처리량

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

사용해 보세요 [Hyper-v 마이그레이션](tutorial-migrate-hyper-v.md) Azure 마이그레이션 서버 마이그레이션을 사용 하 여 합니다.
