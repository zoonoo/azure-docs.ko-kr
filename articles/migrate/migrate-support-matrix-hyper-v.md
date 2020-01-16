---
title: Azure Migrate의 Hyper-v 평가 지원
description: Azure Migrate의 Hyper-v 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028793"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-v 평가를 위한 지원 매트릭스

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 를 사용 하 여 hyper-v vm을 평가 하기 위한 지원 설정 및 제한 사항을 요약 합니다. Hyper-v Vm을 Azure로 마이그레이션하는 방법에 대 한 정보를 찾고 있는 경우 [마이그레이션 지원 매트릭스](migrate-support-matrix-hyper-v-migration.md)를 검토 하세요.

## <a name="overview"></a>개요

이 문서를 사용 하 여 Azure로의 마이그레이션에 대 한 온-프레미스 컴퓨터를 평가 하려면 Azure Migrate: 서버 평가 도구를 Azure Migrate 프로젝트에 추가 합니다. [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포 합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색 하 고 구성 및 성능 데이터를 Azure로 전송 합니다. 컴퓨터 검색 후에는 검색 된 컴퓨터를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한**| 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 35000 hyper-v vm을 검색 하 고 평가 합니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트는 VMware Vm, Hyper-v Vm 및 물리적 서버를 평가 제한까지 포함할 수 있습니다.
**검색** | Azure Migrate 어플라이언스는 5000 Hyper-v Vm을 검색할 수 있습니다.<br/><br/> 어플라이언스는 최대 300 Hyper-v 호스트에 연결할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .



## <a name="hyper-v-host-requirements"></a>Hyper-v 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **호스트 배포**       | Hyper-v 호스트는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **권한**           | Hyper-v 호스트에 대 한 관리자 권한이 필요 합니다. <br/> 또는 관리자 권한을 할당 하지 않으려는 경우 로컬 또는 도메인 사용자 계정을 만들고 이러한 그룹에 사용자를 추가 합니다. 원격 관리 사용자, Hyper-v 관리자 및 성능 모니터 사용자입니다. |
| **호스트 운영 체제** | Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2.<br/> Windows Server 2012를 실행하는 Hyper-V 호스트에 있는 VM은 평가할 수 없습니다. |
| **PowerShell 원격 작업**   | 각 호스트에서를 사용 하도록 설정 해야 합니다. |
| **Hyper-v 복제본**       | Hyper-v 복제본을 사용 하는 경우 (또는 VM 식별자가 같은 vm이 여러 개 있는 경우) Azure Migrate를 사용 하 여 원래 vm과 복제 된 Vm을 모두 검색 하는 경우 Azure Migrate에서 생성 된 평가가 정확 하지 않을 수 있습니다. |


## <a name="hyper-v-vm-requirements"></a>Hyper-v VM 요구 사항

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | Azure에서 지 원하는 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제입니다. |
| **Integration Services**       | 운영 체제 정보를 캡처하기 위해 평가 하는 Vm에서 [hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) 실행 중 이어야 합니다. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 사용 합니다. Hyper-v에 대 한 어플라이언스는 Hyper-v VM에서 실행 되며, Azure Portal에서 다운로드 하는 압축 된 Hyper-v VHD를 사용 하 여 배포 됩니다. 

- Hyper-v에 대 한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---hyper-v) 에 대해 알아봅니다.
- 어플라이언스에서 액세스 해야 하는 [url](migrate-appliance.md#url-access) 에 대해 알아봅니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항이 요약 되어 있습니다.

**디바이스** | **연결**
--- | ---
**기기가** | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위한 포트 44368의 인바운드 연결: ``` https://<appliance-ip-or-name>:44368 ```<br/> Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443, 5671 및 5672의 아웃 바운드 연결
**Hyper-v 호스트/클러스터** | CIM(Common Information Model) (CIM) 세션을 사용 하 여 Hyper-v Vm의 구성과 성능 메타 데이터를 끌어오기 위해 WinRM 포트 5985 (HTTP) 및 5986 (HTTPS)에서 인바운드 연결



## <a name="next-steps"></a>다음 단계

[Hyper-v VM 평가 준비](tutorial-prepare-hyper-v.md)
