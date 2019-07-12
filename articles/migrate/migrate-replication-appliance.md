---
title: Azure Migrate 복제 어플라이언스 아키텍처 | Microsoft Docs
description: Azure Migrate 복제 어플라이언스에 대 한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811441"
---
# <a name="replication-appliance"></a>복제 어플라이언스

이 문서에서는 Azure Migrate에서 사용 하는 복제 어플라이언스에 설명 합니다. 서버 마이그레이션 VMware Vm, 물리적 컴퓨터 및 사설/공용 클라우드 때 Vm을 Azure에는 에이전트 기반 마이그레이션을 사용 하 여 평가 합니다. 

이 도구는에서 사용할 수는 [Azure Migrate](migrate-overview.md) 허브입니다. 허브 평가 및 마이그레이션을 위한 기본 도구를 제공으로 제 3 자 독립 소프트웨어 공급 업체 (Isv) 및 다른 Azure 서비스에서 도구.


## <a name="appliance-overview"></a>어플라이언스 개요

단일 온-프레미스 컴퓨터, VMware VM 또는 물리적 서버 복제 어플라이언스 배포 됩니다. 실행:
- **복제 어플라이언스**: 통신을 조정 하 고 온-프레미스 VMware Vm 및 물리적 서버를 Azure로 복제에 대 한 데이터 복제를 관리 하는 복제 어플라이언스입니다.
- **프로세스 서버**: 복제 어플라이언스에서 기본적으로 설치 되 고 다음을 수행 하는 프로세스 서버:
    - **복제 게이트웨이**: 복제 게이트웨이로 작동 합니다. 복제용으로 설정 된 컴퓨터에서 복제 데이터를 받습니다. 캐싱, 압축 및 암호화를 사용 하 여 복제 데이터를 최적화 하 고 Azure에 전송 합니다.
    - **에이전트 설치 관리자**: 모바일 서비스의 강제 설치를 수행합니다. 이 서비스를 설치 해야 하 고 각각에서 실행 중인 온-프레미스 컴퓨터 마이그레이션에 대 한 복제 하려는 합니다.

## <a name="appliance-deployment"></a>어플라이언스 배포

**배포** | **사용 대상** | **세부 정보**
--- | --- |  ---
VMware VM | 에이전트 기반 마이그레이션을 사용 하 여 Azure 마이그레이션 마이그레이션 도구를 사용 하 여 VMware Vm을 마이그레이션하는 경우 일반적으로 사용 합니다. | Azure Migrate 허브에서 OVA 템플릿을 다운로드 하 고이 정보를 vCenter VM 어플라이언스를 만들려면 서버에 가져옵니다.
물리적 컴퓨터 | VMware 인프라가 없는 경우 온-프레미스 물리적 서버를 마이그레이션할 때 OVA 템플릿을 사용 하는 VMware VM을 만들 수 알지 못하는 경우 사용 합니다. | Azure Migrate 허브에서 소프트웨어 설치 관리자를 다운로드 하 고 어플라이언스 컴퓨터를 설정 하 고 실행 합니다.

## <a name="appliance-deployment-requirements"></a>어플라이언스 배포 요구 사항

[검토](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) 배포 요구 사항입니다.



## <a name="appliance-license"></a>어플라이언스 라이선스
어플라이언스 180 일 동안 유효 하는 Windows Server 2016 평가판 라이선스를 함께 제공 됩니다. 평가 기간이 만료에 가까운 경우에 어플라이언스 VM의 운영 체제 라이선스를 활성화 하는 하거나에 다운로드 하 고 새 어플라이언스를 배포 하는 것이 좋습니다.

## <a name="replication-process"></a>복제 프로세스

1. VM에 대해 복제를 사용하도록 설정하면 지정된 복제 정책을 사용하여 Azure Storage에 초기 복제가 시작됩니다. 
2. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. 초기 복제가 완료 되 면 델타 복제가 시작 됩니다. 컴퓨터에 대 한 변경 내용이 기록 됩니다.
4. 다음과 같이 통신이 발생합니다.
    - Vm 통신 HTTPS 443 포트에서 복제 어플라이언스를 사용 하 여 복제 관리에 대 한 인바운드 합니다.
    - 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure 사용 하 여 복제를 오케스트레이션합니다.
    - Vm 복제 데이터를 HTTPS 9443 포트에서 프로세스 서버 (복제 어플라이언스에서 실행) 인바운드 전송 합니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.
5. 복제 된 데이터를 Azure에서 캐시 저장소 계정에 첫 번째 land를 기록합니다. 이러한 로그를 처리 하 고 데이터를 Azure에 저장 됩니다 관리 디스크입니다.

![아키텍처](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스는 Azure Migrate 허브에서 수동 업그레이드 됩니다. 항상 최신 버전을 실행 하는 것이 좋습니다.

1. Azure에서 마이그레이션 > 서버 > Azure Migrate: 서버 평가, 인프라 서버, 클릭 **구성 서버**합니다.
2. **구성 서버**에 대 한 링크가 나타납니다 **에이전트 버전** 복제 어플라이언스의 새 버전이 제공 된 경우. 
3. 복제 어플라이언스 컴퓨터에 설치 관리자를 다운로드 하 고 업그레이드를 설치 합니다. 설치 관리자는 현재 실행 버전 어플라이언스에서 검색 합니다.
 
## <a name="next-steps"></a>다음 단계

[에 대해 알아봅니다 어떻게](tutorial-assess-vmware.md#set-up-the-appliance-vm) VMware에 대 한 어플라이언스를 설정 합니다.
[에 대해 알아봅니다 어떻게](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) 어플라이언스에 Hyper-v을 설정 합니다.

