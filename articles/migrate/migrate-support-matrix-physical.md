---
title: Azure Migrate 사용 하 여 물리적 서버 평가 지원
description: Azure Migrate를 사용한 물리적 서버 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028769"
---
# <a name="support-matrix-for-physical-server-assessment"></a>물리적 서버 평가를 위한 지원 매트릭스 

[Azure Migrate 서비스](migrate-overview.md) 를 사용 하 여 컴퓨터를 평가 하 고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 온-프레미스 물리적 서버를 평가 하 고 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약 합니다.


## <a name="overview"></a>개요

이 문서를 사용 하 여 Azure로의 마이그레이션에 대 한 온-프레미스 컴퓨터를 평가 하려면 Azure Migrate: 서버 평가 도구를 Azure Migrate 프로젝트에 추가 합니다. [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포 합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색 하 고 구성 및 성능 데이터를 Azure로 전송 합니다. 컴퓨터 검색 후에는 검색 된 컴퓨터를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.

## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한**| 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 최대 35000 대의 물리적 서버를 검색 하 고 평가 합니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트는 VMware Vm, Hyper-v Vm 및 물리적 서버를 평가 제한까지 포함할 수 있습니다.
**검색** | Azure Migrate 어플라이언스는 최대 250 대의 물리적 서버를 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 대의 컴퓨터를 평가할 수 있습니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .




## <a name="physical-server-requirements"></a>물리적 서버 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **물리적 서버 배포**       | 물리적 서버는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **권한**           | **Windows:** 검색에 포함 하려는 모든 Windows 서버에서 로컬 사용자 계정을 설정 합니다. 사용자 계정은 원격 데스크톱 사용자, 성능 모니터 사용자 및 성능 로그 사용자 그룹에 추가 해야 합니다. <br/> **Linux:** 검색 하려는 Linux 서버에 루트 계정이 필요 합니다. |
| **운영 체제** | 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제는 다음을 제외 하 고 지원 됩니다.<br/> Windows Server 2003 <br/> SUSE, Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 사용 합니다. 물리적 서버에 대 한 어플라이언스는 VM 또는 물리적 컴퓨터에서 실행할 수 있습니다. Azure Portal에서 다운로드 하는 PowerShell 스크립트를 사용 하 여 설정 합니다.

- 물리적 서버의 [어플라이언스 요구 사항](migrate-appliance.md#appliance---physical) 에 대해 알아봅니다.
- 어플라이언스에서 액세스 해야 하는 [url](migrate-appliance.md#url-access) 에 대해 알아봅니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항이 요약 되어 있습니다.

**디바이스** | **연결**
--- | ---
**기기가** | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위한 포트 44368의 인바운드 연결: ``` https://<appliance-ip-or-name>:44368 ```<br/> Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443, 5671 및 5672의 아웃 바운드 연결
**물리적 서버** | **Windows:** Windows 서버에서 구성 및 성능 메타 데이터를 가져오기 위해 포트 443, WinRM 포트 5985 (HTTP) 및 5986 (HTTPS)에서 인바운드 연결 <br/> **Linux:**  Linux 서버에서 구성 및 성능 메타 데이터를 가져오기 위해 포트 22 (UDP)에서 인바운드 연결 |


## <a name="next-steps"></a>다음 단계

[물리적 서버 평가를 준비](tutorial-prepare-physical.md)합니다.
