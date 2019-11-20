---
title: Azure Migrate 복제 어플라이언스 아키텍처
description: 에이전트 기반 마이그레이션의 Azure Migrate 복제 어플라이언스에 대 한 개요를 제공 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186550"
---
# <a name="replication-appliance"></a>복제 어플라이언스

이 문서에서는 에이전트 기반 마이그레이션을 사용 하 여 VMware Vm, 물리적 컴퓨터 및 사설/공용 클라우드 Vm을 Azure로 마이그레이션할 때 Azure Migrate: 서버 평가에서 사용 하는 복제 어플라이언스를 설명 합니다. 

이 도구는 [Azure Migrate](migrate-overview.md) 허브에서 사용할 수 있습니다. 허브는 기타 Azure 서비스 및 타사 Isv (독립 소프트웨어 공급 업체)의 도구 뿐만 아니라 평가 및 마이그레이션을 위한 기본 도구를 제공 합니다.


## <a name="appliance-overview"></a>어플라이언스 개요

복제 어플라이언스는 VMware VM 또는 물리적 서버로 단일 온-프레미스 컴퓨터로 배포 됩니다. 다음을 실행 합니다.
- **복제 어플라이언스**: 복제 어플라이언스는 통신을 조정 하 고 온-프레미스 VMware Vm 및 Azure로 복제 되는 물리적 서버에 대 한 데이터 복제를 관리 합니다.
- **프로세스 서버**: 복제 어플라이언스에 기본적으로 설치 되는 프로세스 서버 이며, 다음을 수행 합니다.
    - **복제 게이트웨이**: 복제 게이트웨이 역할을 합니다. 복제를 사용 하도록 설정 된 컴퓨터에서 복제 데이터를 수신 합니다. 또한 캐싱, 압축 및 암호화를 사용 하 여 복제 데이터를 최적화 하 고 Azure에 보냅니다.
    - **에이전트 설치 관리자**: 모바일 서비스의 강제 설치를 수행 합니다. 이 서비스는 마이그레이션을 위해 복제 하려는 각 온-프레미스 컴퓨터에 설치 되 고 실행 되어야 합니다.

## <a name="appliance-deployment"></a>어플라이언스 배포

**배포 이름** | **용도** | **세부 정보**
--- | --- |  ---
VMware VM | 일반적으로 에이전트 기반 마이그레이션과 함께 Azure Migrate 마이그레이션 도구를 사용 하 여 VMware Vm을 마이그레이션할 때 사용 됩니다. | Azure Migrate 허브에서 OVA 템플릿을 다운로드 하 고 vCenter Server으로 가져와 어플라이언스 VM을 만듭니다.
물리적 컴퓨터 | VMware 인프라가 없거나 OVA 템플릿을 사용 하 여 VMware VM을 만들 수 없는 경우 온-프레미스 물리적 서버를 마이그레이션하는 데 사용 됩니다. | Azure Migrate 허브에서 소프트웨어 설치 관리자를 다운로드 하 고 실행 하 여 어플라이언스 컴퓨터를 설정 합니다.

## <a name="appliance-deployment-requirements"></a>어플라이언스 배포 요구 사항

배포 요구 사항을 [검토](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) 합니다.



## <a name="appliance-license"></a>어플라이언스 라이선스
어플라이언스는 180 일 동안 유효한 Windows Server 2016 evaluation 라이선스와 함께 제공 됩니다. 평가 기간이 만료에 근접 한 경우 새 어플라이언스를 다운로드 하 여 배포 하거나 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 것이 좋습니다.

## <a name="replication-process"></a>복제 프로세스

1. VM에 대해 복제를 사용하도록 설정하면 지정된 복제 정책을 사용하여 Azure Storage에 초기 복제가 시작됩니다. 
2. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. 초기 복제가 완료되면 델타 복제가 시작됩니다. 컴퓨터에 대 한 추적 된 변경 내용이 기록 됩니다.
4. 다음과 같이 통신이 발생합니다.
    - Vm은 복제 관리를 위해 HTTPS 443 인바운드 포트의 복제 어플라이언스와 통신 합니다.
    - 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure를 사용 하 여 복제를 오케스트레이션 합니다.
    - Vm은 복제 데이터를 HTTPS 9443 인바운드 포트에서 프로세스 서버 (복제 어플라이언스에서 실행)로 보냅니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.
5. 복제 데이터 로그는 먼저 Azure의 캐시 저장소 계정에 저장 됩니다. 이러한 로그는 처리 되 고 데이터는 Azure 관리 디스크에 저장 됩니다.

![아키텍처](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스는 Azure Migrate 허브에서 수동으로 업그레이드 됩니다. 항상 최신 버전을 실행 하는 것이 좋습니다.

1. Azure Migrate > 서버 > Azure Migrate: 서버 평가, 인프라 서버에서 **구성 서버**를 클릭 합니다.
2. **구성 서버**에서 새 버전의 복제 어플라이언스를 사용할 수 있는 경우 **에이전트 버전** 에 링크가 표시 됩니다. 
3. 설치 관리자를 복제 어플라이언스 컴퓨터에 다운로드 하 고 업그레이드를 설치 합니다. 설치 관리자가 어플라이언스에서 현재 실행 중인 버전을 검색 합니다.
 
## <a name="next-steps"></a>다음 단계

VMware에 대 한 어플라이언스를 설정 하 [는 방법을 알아봅니다](tutorial-assess-vmware.md#set-up-the-appliance-vm) .
Hyper-v에 대 한 어플라이언스를 설정 하 [는 방법을 알아봅니다](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) .

