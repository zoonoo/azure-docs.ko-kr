---
title: Azure Migrate에서 마이그레이션 컴퓨터 마이그레이션 자동화
description: 스크립트를 사용 하 여 많은 수의 컴퓨터를 마이그레이션하는 방법에 대해 설명 Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196357"
---
# <a name="scale-migration-of-vms"></a>Vm의 규모 마이그레이션 

이 문서는 스크립트를 사용 하 여 많은 수의 Vm (가상 머신)을 마이그레이션하는 방법을 이해 하는 데 도움이 됩니다. 마이그레이션을 확장 하려면 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)을 사용 합니다. 

Site Recovery 스크립트는 GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) 리포지토리에서 다운로드할 수 있습니다. 스크립트를 사용 하 여 VMware, AWS, GCP Vm 및 물리적 서버를 Azure의 managed disks로 마이그레이션할 수 있습니다. VM을 물리적 서버로 마이그레이션하는 경우 이 스크립트를 사용하여 Hyper-V VM을 마이그레이션할 수도 있습니다. Azure Site Recovery PowerShell을 활용 하는 스크립트는 [여기](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)에 설명 되어 있습니다.

## <a name="current-limitations"></a>현재 제한 사항
- 대상 VM의 기본 NIC에 대해서만 고정 IP 주소를 지정할 수 있습니다.
- 스크립트는 Azure 하이브리드 혜택 관련 입력을 허용하지 않으므로 포털에서 복제된 VM의 속성을 수동으로 업데이트해야 합니다.

## <a name="how-does-it-work"></a>작동 원리

### <a name="prerequisites"></a>선행 조건
시작하기 전에 다음 단계를 수행해야 합니다.
- Site Recovery 자격 증명 모음이 Azure 구독에 생성되었는지 확인
- 구성 서버 및 프로세스 서버가 원본 환경에 설치되었으며 자격 증명 모음이 환경을 검색할 수 있는지 확인
- 복제 정책이 생성되어 구성 서버와 연결되었는지 확인
- VM 관리자 계정을 구성 서버 (온-프레미스 Vm을 복제 하는 데 사용 됨)에 추가 했는지 확인 합니다.
- Azure의 대상 아티팩트가 만들어졌는지 확인
    - 대상 리소스 그룹
    - 대상 저장소 계정 (및 해당 리소스 그룹)-프리미엄 관리 디스크로 마이그레이션할 계획인 경우 premium Storage 계정 만들기
    - 캐시 저장소 계정 및 해당 리소스 그룹-자격 증명 모음과 동일한 지역에 표준 저장소 계정을 만듭니다.
    - 장애 조치(failover)를 위한 대상 가상 네트워크(및 해당 리소스 그룹)
    - 대상 서브넷
    - 테스트 장애 조치(failover)를 위한 대상 가상 네트워크(및 해당 리소스 그룹)
    - 가용성 집합(필요한 경우)
    - 대상 네트워크 보안 그룹 및 해당 리소스 그룹
- 대상 VM의 속성을 결정했는지 확인합니다.
    - 대상 VM 이름
    - Azure의 대상 VM 크기(Azure Migrate 평가를 사용하여 결정할 수 있음)
    - VM에 있는 기본 NIC의 개인 IP 주소
- GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) 리포지토리에서 스크립트 다운로드

### <a name="csv-input-file"></a>CSV 입력 파일
모든 필수 사항을 완료했으면 마이그레이션하려는 각 원본 컴퓨터에 대한 데이터가 있는 CSV 파일을 만들어야 합니다. 입력 CSV에는 입력 정보가 포함된 헤더 줄과 마이그레이션해야 하는 각 머신에 대한 세부 정보가 포함된 행이 있어야 합니다. 모든 스크립트가 동일한 CSV 파일을 사용하도록 설계되었습니다. 참조용 샘플 CSV 템플릿이 스크립트 폴더에 제공됩니다.

### <a name="script-execution"></a>스크립트 실행
CSV가 준비되면 다음 단계를 실행하여 온-프레미스 VM을 마이그레이션할 수 있습니다.

**단계 #** | **스크립트 이름** | **설명**
--- | --- | ---
1 | asr_startmigration.ps1 | csv에 나열된 모든 VM에 대해 복제를 사용하도록 설정합니다. 이 스크립트는 각 VM에 대한 작업 정보가 포함된 CSV 출력을 만듭니다.
2 | asr_replicationstatus.ps1 | 복제 상태를 확인합니다. 이 스크립트는 각 VM의 상태가 포함된 csv를 만듭니다.
3 | asr_updateproperties.ps1 | VM이 복제/보호되고 나면, 이 스크립트를 사용하여 VM의 대상 속성(컴퓨팅 및 네트워크 속성)을 업데이트합니다.
4 | asr_propertiescheck.ps1 | 속성이 제대로 업데이트되었는지 확인합니다.
5 | asr_testmigration.ps1 |  csv에 나열된 VM의 테스트 장애 조치(failover)를 시작합니다. 이 스크립트는 각 VM에 대한 작업 정보가 포함된 CSV 출력을 만듭니다.
6 | asr_cleanuptestmigration.ps1 | 테스트 장애 조치(failover)된 VM의 유효성을 수동으로 검사한 후 이 스크립트를 사용하여 테스트 장애 조치(failover) VM을 정리할 수 있습니다.
7 | asr_migration.ps1 | csv에 나열된 VM에 대해 계획되지 않은 장애 조치를 수행하면 스크립트는 각 VM에 대한 작업 세부 정보가 있는 CSV 출력을 만듭니다. 스크립트는 응용 프로그램의 일관성을 위해 장애 조치(failover)를 트리거하기 전에 온 프레미스 VM을 종료하지 않으므로 스크립트를 실행하기 전에 VM을 수동으로 종료하는 것이 좋습니다.
8 | asr_completemigration.ps1 | VM에서 커밋 작업을 수행하고 Azure Site Recovery 엔터티 삭제
9 | asr_postmigration.ps1 | 장애 조치(failover) 후 NIC에 네트워크 보안 그룹을 할당하려는 경우 이 스크립트를 사용하면 됩니다. 대상 VM의 단일 NIC에 NSG를 할당 합니다.

## <a name="how-to-migrate-to-managed-disks"></a>관리 디스크로 마이그레이션하는 방법
스크립트는 기본적으로 VM에서 Azure 관리 디스크로 마이그레이션합니다. 제공된 대상 저장소 계정이 프리미엄 저장소 계정인 경우 마이그레이션 후 프리미엄 관리 디스크가 생성됩니다. 캐시 저장소 계정은 표준 계정일 수 있습니다. 대상 저장소 계정이 표준 저장소 계정인 경우 마이그레이션 후 표준 디스크가 만들어집니다. 

## <a name="next-steps"></a>다음 단계

Azure Site Recovery를 사용하여 서버를 Azure로 마이그레이션하는 방법에 대한 [자세한 정보](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)
