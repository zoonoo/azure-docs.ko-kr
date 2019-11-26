---
title: Azure Migrate에서 Hyper-v 마이그레이션이 어떻게 작동 하나요?
description: Azure Migrate를 사용 하 여 Hyper-v 마이그레이션에 대해 알아보기
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185870"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-v 복제는 어떻게 작동 하나요?

이 문서에서는 Azure Migrate 서버 마이그레이션 도구를 사용 하 여 Hyper-v Vm을 마이그레이션할 때 사용 되는 아키텍처와 프로세스에 대 한 개요를 제공 합니다.

[Azure Migrate](migrate-services-overview.md)는 온-프레미스 앱과 워크로드 및 프라이빗/퍼블릭 클라우드 VM의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

## <a name="agentless-migration"></a>에이전트 없는 마이그레이션

Azure Migrate 서버 마이그레이션 도구는 Hyper-v에 최적화 된 마이그레이션 워크플로를 사용 하 여 온-프레미스 Hyper-v Vm에 대 한 에이전트 없는 복제를 제공 합니다. 소프트웨어 에이전트는 Hyper-v 호스트나 클러스터 노드에만 설치 합니다. Hyper-v Vm에는 아무 것도 설치할 필요가 없습니다.

## <a name="server-migration-and-azure-site-recovery"></a>서버 마이그레이션 및 Azure Site Recovery

Azure Migrate Server 마이그레이션은 온-프레미스 워크 로드 및 클라우드 기반 Vm을 Azure로 마이그레이션하기 위한 도구입니다. Site Recovery은 재해 복구 도구입니다. 이 도구는 데이터 복제에 사용 되는 몇 가지 일반적인 기술 구성 요소를 공유 하지만 다른 용도로 사용 됩니다. 


## <a name="architectural-components"></a>아키텍처 구성 요소

![아키텍처](./media/hyper-v-replication-architecture/architecture.png)



**구성 요소** | **배포웹사이트를** | 
--- | --- 
**복제 공급자** | Microsoft Azure Site Recovery 공급자는 Hyper-v 호스트에 설치 되 고 Azure 마이그레이션 서버 마이그레이션에 등록 됩니다.<br/> 공급자는 Hyper-v Vm에 대 한 복제를 오케스트레이션 합니다.
**Recovery Services 에이전트** | Microsoft Azure 복구 서비스 에이전트는 데이터 복제를 처리 합니다. 공급자와 함께 작동 하 여 Hyper-v Vm에서 Azure로 데이터를 복제 합니다.<br/> 복제 된 데이터는 Azure 구독의 저장소 계정에 업로드 됩니다. 서버 마이그레이션 도구는 복제 된 데이터를 처리 하 고 구독의 복제본 디스크에 적용 합니다. 복제 디스크는 마이그레이션할 때 Azure Vm을 만드는 데 사용 됩니다.

- 구성 요소는 단일 설치 파일에 의해 설치 되며 포털의 Azure Migrate Server 마이그레이션에서 다운로드 됩니다.
- 공급자 및 어플라이언스는 아웃 바운드 HTTPS 포트 443 연결을 사용 하 여 Azure Migrate 서버 마이그레이션과 통신 합니다.
- 공급자 및 에이전트의 통신은 안전 하 고 암호화 됩니다.


## <a name="replication-process"></a>복제 프로세스

1. Hyper-v VM에 대해 복제를 사용 하도록 설정 하면 초기 복제가 시작 됩니다.
2. Hyper-v VM 스냅숏이 생성 됩니다.
3. VM의 Vhd는 모두 Azure에 복사 될 때까지 하나씩 복제 됩니다. 초기 복제 시간은 VM 크기 및 네트워크 대역폭에 따라 달라 집니다.
4. 초기 복제 중에 발생 하는 디스크 변경은 Hyper-v 복제본을 사용 하 여 추적 되 고 로그 파일 (hrl 파일)에 저장 됩니다.
    - 로그 파일은 디스크와 동일한 폴더에 있습니다.
    - 각 디스크에는 보조 저장소로 전송 되는 연결 된 .hrl 파일이 있습니다.
    - 초기 복제 진행 중에는 스냅샷과 로그 파일이 디스크 리소스를 사용합니다.
4. 초기 복제가 완료 되 면 VM 스냅숏이 삭제 되 고 델타 복제가 시작 됩니다.
5. 증분 디스크 변경 내용은 .hrl 파일에서 추적 됩니다. 복제 로그는 Recovery Services 에이전트가 Azure storage 계정에 주기적으로 업로드 됩니다.


## <a name="performance-and-scaling"></a>성능 및 크기 조정

Hyper-v에 대 한 복제 성능은 VM 크기, vm의 데이터 변경 률 (이탈), 로그 파일 저장소에 대 한 Hyper-v 호스트에서 사용 가능한 공간, 복제 데이터에 대 한 업로드 대역폭 및 Azure의 대상 저장소를 포함 하는 요인의 영향을 받습니다.

- 동시에 여러 컴퓨터를 복제 하는 경우 Hyper-v에 대 한 [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) 를 사용 하 여 복제를 최적화 합니다.
- Hyper-v 복제를 계획 하 고 용량에 따라 Azure storage 계정에 복제를 배포 합니다.

### <a name="control-upload-throughput"></a>업로드 처리량 제어

각 Hyper-v 호스트에서 Azure로 데이터를 업로드 하는 데 사용 되는 대역폭의 양을 제한할 수 있습니다. 조심하세요. 값을 너무 낮게 설정 하면 복제에 영향을 주지 않고 마이그레이션을 지연 시킵니다.


1. Hyper-v 호스트 또는 클러스터 노드에 로그인 합니다.
2. **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**를 실행 하 여 WINDOWS Azure Backup MMC 스냅인을 엽니다.
3. 스냅인에서 **속성 변경**을 선택합니다.
4. **제한**에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택 합니다. 작업 시간 및 비 작업 시간의 제한을 설정합니다. 유효 범위는 512Kbps~1,023Mbps입니다.
I

### <a name="influence-upload-efficiency"></a>업로드 효율성에 영향을 줍니다.

복제에 대 한 스페어 대역폭이 있고 업로드를 늘리려면 업로드 작업에 할당 된 스레드 수를 다음과 같이 늘릴 수 있습니다.

1. Regedit를 사용 하 여 레지스트리를 엽니다.
2. 키 HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM로 이동 합니다.
3. 복제 하는 각 VM에 대 한 데이터 업로드에 사용 되는 스레드 수의 값을 늘립니다. 기본값은 4이 고 최대값은 32입니다. 




## <a name="next-steps"></a>다음 단계

Azure Migrate Server 마이그레이션을 사용 하 여 [hyper-v 마이그레이션을](tutorial-migrate-hyper-v.md) 사용해 보세요.
