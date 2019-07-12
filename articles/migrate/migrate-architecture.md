---
title: Azure Migrate 아키텍처 | Microsoft Docs
description: Azure Migrate 서비스의 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811428"
---
# <a name="azure-migrate-architecture-and-processes"></a>Azure Migrate 아키텍처 및 프로세스

[Azure Migrate](migrate-overview.md) 는 허브를 검색, 평가 및 Microsoft Azure에 앱, 인프라 및 워크 로드를 마이그레이션할 수 있도록 도구를 제공 합니다. 허브는 Azure Migrate 도구 및 타사 독립 소프트웨어 공급 업체 (ISV) 제품에 포함 되어 있습니다. 

 이 문서에서는 Azure Migrate Server 평가 및 Azure 마이그레이션 서버 마이그레이션에 대 한 평가 및 마이그레이션 아키텍처와 프로세스를 요약 합니다.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Azure 사용 하 여 평가 마이그레이션할 서버 평가

Azure Migrate Server 평가 Azure로의 마이그레이션을 위해 VMware Vm 및 Hyper-v Vm을 평가할 수 있습니다. 평가 다음과 같이 작동합니다.

1. **평가 준비**: 온-프레미스에서 Azure Migrate로 간단한 Azure Migrate 어플라이언스 온-프레미스 VMware VM 또는 Hyper-v VM 및 등록 어플라이언스로 설정 합니다.
2. **Vm 검색**: Azure Migrate 어플라이언스 온-프레미스 Vm 검색을 실행 합니다. 
    - 검색 된 Vm에 설치 해야 하는 아무 작업도 수행 합니다.
    - VM 메타 데이터에는 코어, 메모리, 디스크, 디스크 크기 및 네트워크 어댑터에 대 한 정보가 포함 됩니다.
    - CPU 및 메모리 사용량, 디스크 IOPS, 디스크 처리량 (MBps) 및 네트워크 출력 (MBps)에 대 한 정보를 포함 하는 성능 데이터
- **수집 하 고 컴퓨터를 평가**: 검색에는 다음이 완료 되 면 Azure에서 수집 하는 포털 검색 Vm 일반적으로 함께 마이그레이션할 하려는 Vm으로 구성 된 그룹으로 합니다. 그룹에서 평가 실행 합니다.


## <a name="vmware-assessment"></a>VMware 평가 

다이어그램은 Azure Migrate Server 평가 사용 하 여 VMware VM 평가 작동 하는 방법을 요약 합니다.

![VMware 평가 아키텍처](./media/migrate-architecture/sas-architecture-vmware.png)

프로세스는 다음과 같습니다.

1. **Azure Migrate 어플라이언스를 배포**:

    a. Azure portal에서 (OVA) 템플릿을 다운로드 합니다.

    b. VM을 만들려면 vCenter Server 컴퓨터로 가져옵니다.

    c. 이 VM에 연결 하 고,에 대 한 기본 설정을 구성 합니다. Azure Migrate로 등록 합니다.

2. **시작 검색**:

    a. 검색을 시작 하 여 장치에서 실행 되는 수집기 앱에 연결 합니다.

    b. 어플라이언스는 지속적으로 azure Vm에서 메타 데이터 및 성능 데이터를 보냅니다.

    - 어플라이언스는 Azure Migrate 서비스에 항상 연결 됩니다.
    - 연속 검색 하는 동안 환경 변경 사항이 캡처됩니다. 예를 들어 검색 범위에서 Vm을 추가 하거나 VM 디스크 또는 Nic를 추가 합니다.

3. **컴퓨터를 평가**:

    a. 검색에는 다음이 완료 되 면 Azure에서 수집 하는 포털 검색 Vm 그룹화.  일반적으로 그룹을 함께 마이그레이션해야 하 고 싶은 vm으로 구성 됩니다.

    b. 각 그룹에 대 한 평가 실행 합니다.

4. **평가 검토**: 

    a. 평가가 완료 되 면 포털에서 확인 합니다.

    b. 추가 분석을 위해 Excel 형식으로의 평가 다운로드 합니다.



### <a name="vmware-ports"></a>VMware 포트
Azure Migrate는 VMware에 대 한 연결 포트를 사용합니다.

**원본** | **대상** | **포트** | **세부 정보**
--- | --- | --- | ---
Azure Migrate 어플라이언스 | Azure Migrate 서비스 | Target-TCP 443 | Azure Migrate에 메타 데이터 및 성능 데이터를 보냅니다.
Azure Migrate 어플라이언스 | vCenter Server | Target-TCP 443 | 메타 데이터 및 성능 데이터에 대 한 vCenter Server에 연결 합니다. 443 기본값 이지만 다른 포트에서 수신 vCenter 사용 하 여 수정할 수 있습니다. 
RDP 클라이언트 | Azure Migrate 어플라이언스 | Target-TCP3389 | 어플라이언스에서 트리거 검색에 RDP 연결 합니다.

### <a name="collected-vmware-metadata"></a>수집 된 VMware 메타 데이터

어플라이언스를 Azure Vm에서 메타 데이터 및 성능 관련 데이터를 보냅니다.

**동작** | **세부 정보**
--- | ---
**수집 된 메타 데이터** | vCenter VM 이름<br/> vCenter VM 경로 (호스트/클러스터 폴더)<br/> IP 및 MAC 주소<br/> 운영 체제<br/> Nic/디스크/코어 수<br/> 메모리 및 디스크 크기입니다.
**수집 된 성능 데이터** | P U/메모리 사용량<br/> 디스크 데이터 (디스크 읽기/쓰기 처리량, 초당 디스크 읽기/쓰기) 당<br/> NIC (네트워크 외부에서 네트워크)을 데이터입니다.<br/><br/> 성능 데이터는 어플라이언스는 vCenter Server에 연결 된 후에 지속적으로 수집 됩니다. 기록 데이터를 수집 하지 않습니다.

## <a name="hyper-v-assessment"></a>Hyper-v 평가

다이어그램은 Hyper-v 평가 works Azure Migrate Server 평가 로그온 하는 방법을 요약 합니다.

![평가 아키텍처 Hyper-v](./media/migrate-architecture/sas-architecture-hyper-v.png)

프로세스는 다음과 같습니다.

1. **Azure Migrate 어플라이언스 만들기**:

    a. 압축 된 형식에서 VM을 Azure portal에서 다운로드 합니다.

    b. Hyper-v 호스트를 가져옵니다.

    c. 어플라이언스 VM에 연결 합니다. 하지만 기본 설정을 구성 하 고 Azure Migrate로 등록 합니다.

2. **시작 검색**:

    a. 검색을 시작 하려면 Azure Migrate 어플라이언스에 연결 합니다. 검색 된 Vm에 설치 해야 하는 아무 작업도 수행 합니다.

    b. 어플라이언스는 지속적으로 Azure Migrate에 VM 메타 데이터 및 성능 데이터를 보냅니다.

    - 어플라이언스 (CIM 세션을 사용 하 여) Azure Migrate 서비스에 항상 연결 되어 있습니다.
    - 연속 검색 하는 동안 환경 변경 사항이 캡처됩니다. 예를 들어 검색 범위에서 Vm을 추가 하거나 VM 디스크 또는 Nic를 추가 합니다.


3. **컴퓨터를 평가**:

    a. 검색에는 다음이 완료 되 면 Azure에서 수집 하는 포털 검색 Vm 그룹화.  일반적으로 그룹을 함께 마이그레이션해야 하 고 싶은 vm으로 구성 됩니다.

    b. 각 그룹에 대 한 평가 실행 합니다.

4. **평가 검토**:

    a. 평가가 완료 되 면 포털에서 확인 합니다.

    b. 추가 분석을 위해 Excel 형식으로의 평가 다운로드 합니다.

### <a name="hyper-v-ports"></a>Hyper-v 포트

Hyper-v에 대 한 다음 연결 포트를 사용 하는 azure Migrate 서비스

**원본** | **대상** | **포트** | **세부 정보**
--- | --- | --- | ---
Azure Migrate 어플라이언스 | Azure Migrate 서비스 | Target-TCP 443 | Azure Migrate에 메타 데이터 및 성능 데이터를 보냅니다.
Azure Migrate 어플라이언스 | Hyper-v 호스트/클러스터 | 대상 기본 WinRM 포트-HTTP:5985; HTTPS:5986 | 메타 데이터 및 성능 데이터에 대 한 호스트에 연결 합니다. CIM 세션 연결에 사용
RDP 클라이언트 | Azure Migrate 어플라이언스 | Target-TCP3389 | 어플라이언스에서 트리거 검색에 RDP 연결 합니다.

## <a name="collected-hyper-v-vm-metadata"></a>수집 된 Hyper-v VM 메타 데이터

어플라이언스를 Azure Vm에서 메타 데이터 및 성능 관련 데이터를 보냅니다.


**동작** | **세부 정보**
--- | ---
**수집 된 메타 데이터** | VM 이름<br/> VM 경로 (호스트/클러스터 폴더)<br/> IP 및 MAC 주소<br/> 운영 체제<br/> Nic/디스크/코어 수<br/> 메모리 및 디스크 크기<br/> 디스크 IOPS, 디스크 처리량 (MBps) 네트워크 출력 (MBps)
**수집 된 성능 데이터** |  P U/메모리 사용량<br/> 디스크 데이터 (디스크 읽기/쓰기 처리량, 초당 디스크 읽기/쓰기) 당<br/> NIC (네트워크 외부에서 네트워크)을 데이터입니다.<br/><br/> 성능 데이터는 어플라이언스에 Hyper-v 호스트에 연결 된 후에 지속적으로 수집 됩니다. 기록 데이터를 수집 하지 않습니다.




## <a name="migration-with-azure-migrate-server-migration"></a>Azure로 마이그레이션할 서버 마이그레이션

Azure 마이그레이션 서버 마이그레이션을 사용 하 여, 다음 Azure에 마이그레이션할 수 있습니다.

- 온-프레미스 VMware VM
- 온-프레미스 Hyper-v Vm
- 온-프레미스 물리적 서버
- AWS Windows VM 인스턴스
- GCP Windows VM 인스턴스

마이그레이션 프로세스는 약간 다른, 마이그레이션하는 작업에 따라 합니다.


## <a name="migrate-vmware-vms"></a>VMware Vm 마이그레이션

온-프레미스 VMware Vm 마이그레이션에 대 한 두 가지 방법을 제공 하는 azure 마이그레이션 서버 마이그레이션:

- **에이전트 없는 복제**: 에 아무 것도 설치 하지 않고도 Vm을 마이그레이션하십시오.
- **복제 에이전트 기반**입니다. 복제를 위해 VM에 에이전트를 설치 합니다.

에이전트 없는 복제 하는 것이 배포 관점에서에서 더 쉽지만, 많은 제한 사항이 갖고 있습니다. [자세한](server-migrate-overview.md) 이러한 제한에 대 한 합니다.
 

### <a name="agent-based-migration"></a>에이전트 기반 마이그레이션

VMware Vm의 마이그레이션을 에이전트 기반 테이블에 요약 된 것 처럼 다양 한 배포 해야 하는 구성 요소 필요 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**구성 서버 컴퓨터** | 단일 온-프레미스 VMware VM을 다운로드 한 OVF 템플릿에서 배포 되는 합니다.<br/><br/> 컴퓨터는 모든 온-프레미스 Site Recovery 구성 요소를 구성 서버 및 프로세스 서버를 포함 하는 실행 합니다. | **구성 서버**: 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.<br/><br/> **프로세스 서버**: 기본적으로 구성 서버에 설치합니다. 수신한 복제 데이터 캐싱, 압축 및 암호화를 사용 하 여 최적화 Azure에 전송 하 고 있습니다. 또한 프로세스 서버는 Vm에서 Azure Site Recovery Mobility Service를 설치 하 고 온-프레미스 컴퓨터의 자동 검색을 수행 합니다.
**모바일 서비스** | 모바일 서비스를 복제 하는 각 VMware VM에 설치 되어야 합니다. | 프로세스 서버에서 자동 설치를 수행할 수 있도록 하는 것이 좋습니다. 또는 서비스를 수동으로 설치하거나 System Center Configuration Manager와 같은 자동화된 배포 방법을 사용할 수 있습니다.





### <a name="agent-based-replication-process"></a>복제 에이전트 기반 프로세스

![복제 프로세스](./media/migrate-architecture/v2a-architecture-henry.png)

1. VM의 복제를 사용 하도록 설정 하면 Azure에 초기 복제 시작 합니다. 
    - 복제에는 블록 수준, 거의 연속적인, VM에서 실행 중인 모바일 서비스 에이전트를 사용 하는입니다.
    - 복제 정책 설정은 적용 됩니다.
        - **RPO 임계값**: 이 설정은 복제에 영향을 주지 않습니다. 모니터링에 도움이 됩니다. 현재 RPO가 지정 임계값 한도를 초과하는 경우 이벤트가 발생하고 선택적으로 메일이 전송됩니다.
        - **복구 지점 보존**: 이 설정은 중단이 발생하는 경우 얼마나 오래전으로 되돌아갈지 지정합니다. Premium Storage의 최대 보존 기간은 24시간입니다. 표준 저장소의 경우는 72시간입니다. 
        - **앱 일치 스냅샷**. 앱의 요구 사항에 따라 1시간에서 12시간마다 앱 일치 스냅샷을 만들 수 있습니다. 스냅샷은 표준 Azure Blob 스냅샷입니다. VM에서 실행되는 모바일 에이전트는 이 설정에 따라 VSS 스냅샷을 요청하며 이 특정 시점을 복제 스트림의 애플리케이션 일치 지점으로 북마크합니다.

2. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다.

    - 또는 사용 하 여 Azure ExpressRoute를 사용할 수 있습니다 [Microsoft 피어 링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)합니다.
    - 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 머신의 추적된 변경 내용이 프로세스 서버로 전송됩니다.
2. 다음과 같이 통신이 발생합니다.

    - VM은 복제 관리를 위해 HTTPS 443 인바운드 포트에 대한 온-프레미스 구성 서버와 통신합니다.
    - 구성 서버는 HTTPS 443 아웃바운드 포트를 통해 Azure를 사용하는 복제를 오케스트레이션합니다.
    - VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.
5. 복제 된 데이터를 Azure에서 캐시 저장소 계정에 첫 번째 land를 기록합니다. 이러한 로그를 처리 하 고 데이터를 Azure에 저장 됩니다 관리 되는 디스크 (Azure Site Recovery 초기값 디스크). 이 디스크에서 복구 지점이 생성 됩니다.







## <a name="next-steps"></a>다음 단계

- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).
- 커뮤니티에서 도움말을 참조 하세요. 합니다 [Azure 마이그레이션 MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) 또는 [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate)합니다.

