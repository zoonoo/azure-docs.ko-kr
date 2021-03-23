---
title: Azure Migrate 어플라이언스 아키텍처
description: 서버 검색, 평가 및 마이그레이션에 사용 되는 Azure Migrate 어플라이언스에 대 한 개요를 제공 합니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: f3a94576ef58eabf9d747c6e6c3a6372569d4cf1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785243"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 어플라이언스 아키텍처

이 문서에서는 Azure Migrate 어플라이언스 아키텍처와 프로세스에 대해 설명 합니다. Azure Migrate 어플라이언스는 온-프레미스에 배포 되는 경량 어플라이언스로, Azure로 마이그레이션하도록 서버를 검색 합니다.

## <a name="deployment-scenarios"></a>배포 시나리오

Azure Migrate 어플라이언스가 사용되는 시나리오는 다음과 같습니다.

**시나리오** | **도구** | **사용 대상**
--- | --- | ---
**VMware 환경에서 실행 중인 서버 검색 및 평가** | Azure Migrate: 검색 및 평가 | VMware 환경에서 실행 중인 서버 검색<br/><br/> 설치 된 소프트웨어 인벤토리에 대 한 검색을 수행 하 고, 에이전트 없는 종속성 분석을 수행 하 고 SQL Server 인스턴스와 데이터베이스를 검색 합니다.<br/><br/> 평가에 대 한 서버 구성 및 성능 메타 데이터를 수집 합니다.
**VMware 환경에서 실행 되는 서버에 대 한 에이전트 없는 마이그레이션** | Azure Migrate: 서버 마이그레이션 | VMware 환경에서 실행 중인 서버를 검색 합니다.<br/><br/> 서버에 에이전트를 설치 하지 않고 서버를 복제 합니다.
**Hyper-v 환경에서 실행 중인 서버 검색 및 평가** | Azure Migrate: 검색 및 평가 | Hyper-v 환경에서 실행 중인 서버를 검색 합니다.<br/><br/> 평가에 대 한 서버 구성 및 성능 메타 데이터를 수집 합니다.
**온-프레미스에서 물리적 또는 가상화 된 서버 검색 및 평가** |  Azure Migrate: 검색 및 평가 |  온-프레미스에서 물리적 또는 가상화 된 서버를 검색 합니다.<br/><br/> 평가에 대 한 서버 구성 및 성능 메타 데이터를 수집 합니다.

## <a name="deployment-methods"></a>배포 방법

어플라이언스는 다음과 같은 몇 가지 방법을 사용하여 배포할 수 있습니다.

- VMware 또는 Hyper-v 환경 ([vmware 용 Ova 템플릿](how-to-set-up-appliance-vmware.md) 또는 [hyper-v 용 VHD](how-to-set-up-appliance-hyper-v.md))에서 실행 되는 서버에 대 한 템플릿을 사용 하 여 어플라이언스를 배포할 수 있습니다.
- 템플릿을 사용 하지 않으려는 경우 [PowerShell 설치 관리자 스크립트](deploy-appliance-script.md)를 사용 하 여 VMware 또는 hyper-v 환경용 어플라이언스를 배포할 수 있습니다.
- Azure Government에서 PowerShell 설치 관리자 스크립트를 사용 하 여 어플라이언스를 배포 해야 합니다. [여기](deploy-appliance-script-government.md)에서 배포 단계를 참조 하세요.
- 온-프레미스 또는 다른 클라우드의 물리적 또는 가상화 된 서버에 대해서는 항상 PowerShell 설치 관리자 스크립트를 사용 하 여 어플라이언스를 배포 합니다. [여기](how-to-set-up-appliance-physical.md)에서 배포 단계를 참조 하세요.
- 다운로드 링크는 아래 표에 나와 있습니다.

## <a name="appliance-services"></a>어플라이언스 서비스

어플라이언스에는 다음과 같은 서비스가 있습니다.

- **어플라이언스 구성 관리자**: 서버에 대 한 검색 및 평가를 시작 하기 위해 원본 세부 정보를 사용 하 여 구성할 수 있는 웹 응용 프로그램입니다. 
- **검색 에이전트**: 에이전트는 온-프레미스 평가로 만드는 데 사용할 수 있는 서버 구성 메타 데이터를 수집 합니다.
- **평가 에이전트**: 에이전트는 성능 기반 평가를 만드는 데 사용할 수 있는 서버 성능 메타 데이터를 수집 합니다.
- **자동 업데이트 서비스**: 서비스는 어플라이언스에서 실행 되는 모든 에이전트를 최신 상태로 유지 합니다. 자동으로 24 시간 마다 한 번씩 실행 됩니다.
- **DRA 에이전트**: 서버 복제를 오케스트레이션 하 고 복제 된 서버와 Azure 간의 통신을 조정 합니다. 에이전트 없는 마이그레이션을 사용 하 여 Azure에 서버를 복제 하는 경우에만 사용 됩니다.
- **게이트웨이**: 복제된 데이터를 Azure에 보냅니다. 에이전트 없는 마이그레이션을 사용 하 여 Azure에 서버를 복제 하는 경우에만 사용 됩니다.
- **SQL 검색 및 평가 에이전트**: SQL Server 인스턴스 및 데이터베이스의 구성 및 성능 메타 데이터를 Azure에 보냅니다.

> [!Note]
> 지난 3 서비스는 VMware 환경에서 실행 중인 서버를 검색 하 고 평가 하는 데 사용 되는 어플라이언스 에서만 사용할 수 있습니다.<br/> VMware 환경에서 실행되는 SQL Server 인스턴스 및 데이터베이스를 검색하고 평가하는 기능은 현재 미리 보기로 제공됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL)를 사용하여 **오스트레일리아 동부** 지역에서 프로젝트를 만듭니다. 오스트레일리아 동부에 이미 프로젝트가 있고 이 기능을 사용해 보려는 경우 포털에서 이러한 [**필수 구성 요소**](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

## <a name="discovery-and-collection-process"></a>검색 및 수집 프로세스

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="어플라이언스 아키텍처":::

어플라이언스는 다음 프로세스를 사용 하 여 검색 원본과 통신 합니다.

**처리** | **VMware 어플라이언스** | **Hyper-v 어플라이언스** | **물리적 어플라이언스**
---|---|---|---
**검색 시작** | 어플라이언스는 기본적으로 TCP 포트 443의 vCenter server와 통신 합니다. VCenter 서버가 다른 포트에서 수신 대기 하는 경우 어플라이언스 구성 관리자에서 구성할 수 있습니다. | 어플라이언스는 WinRM 포트 5985 (HTTP)에서 Hyper-v 호스트와 통신 합니다. | 어플라이언스는 포트 22 (TCP)를 통해 Linux 서버를 통해 WinRM 포트 5985 (HTTP)를 통해 Windows 서버와 통신 합니다.
**구성 및 성능 메타 데이터 수집** | 어플라이언스는 포트 443 (기본 포트) 또는에서 수신 대기 vCenter Server 다른 포트에서 연결 하 여 vSphere Api를 사용 하 여 vCenter Server에서 실행 되는 서버의 메타 데이터를 수집 합니다. | 어플라이언스는 포트 5985의 호스트가 포함 된 CIM(Common Information Model) (CIM) 세션을 사용 하 여 Hyper-v 호스트에서 실행 되는 서버의 메타 데이터를 수집 합니다.| 어플라이언스는 포트 5985의 서버와 함께 CIM(Common Information Model) (CIM) 세션을 사용 하 여 Windows 서버에서 메타 데이터를 수집 하 고 포트 22에서 SSH 연결을 사용 하 여 Linux 서버
**검색 데이터 보내기** | 어플라이언스는 수집 된 데이터를 Azure Migrate: 검색 및 평가 및 Azure Migrate: SSL 포트 443을 통한 서버 마이그레이션으로 보냅니다.<br/><br/> 어플라이언스는 인터넷을 통해 또는 Express 경로를 통해 Azure에 연결할 수 있습니다 (Microsoft 피어 링 필요). | 어플라이언스는 수집 된 데이터를 Azure Migrate으로 보냅니다. SSL 포트 443을 통해 검색 및 평가 합니다.<br/><br/> 어플라이언스는 인터넷을 통해 또는 Express 경로를 통해 Azure에 연결할 수 있습니다 (Microsoft 피어 링 필요).| 어플라이언스는 수집 된 데이터를 Azure Migrate으로 보냅니다. SSL 포트 443을 통해 검색 및 평가 합니다.<br/><br/> 어플라이언스는 인터넷을 통해 또는 Express 경로를 통해 Azure에 연결할 수 있습니다 (Microsoft 피어 링 필요).
**데이터 수집 빈도** | 구성 메타 데이터는 30 분 마다 수집 되 고 전송 됩니다. <br/><br/> 성능 메타 데이터는 20 초 마다 수집 되 고 10 분 마다 Azure로 데이터 요소를 전송 하도록 집계 됩니다. <br/><br/> 소프트웨어 인벤토리 데이터는 12 시간 마다 한 번씩 Azure에 전송 됩니다. <br/><br/> 에이전트 없는 종속성 데이터는 어플라이언스로 집계 되 고 6 시간 마다 Azure로 전송 되는 5 분 마다 수집 됩니다. <br/><br/> SQL Server 구성 데이터는 24 시간 마다 업데이트 되며 성능 데이터는 30 초 마다 캡처됩니다.| 구성 메타 데이터는 30 분 마다 수집 되 고 전송 됩니다. <br/><br/> 성능 메타 데이터는 30 초 마다 수집 되 고 10 분 마다 Azure로 데이터 요소를 전송 하도록 집계 됩니다.|  구성 메타 데이터는 30 분 마다 수집 되 고 전송 됩니다. <br/><br/> 성능 메타 데이터는 5 분 마다 수집 되 고 10 분 마다 Azure로 데이터 요소를 전송 하도록 집계 됩니다.
**평가 및 마이그레이션** | Azure Migrate: 검색 및 평가 도구를 사용 하 여 어플라이언스에서 수집한 메타 데이터에서 평가를 만들 수 있습니다.<br/><br/>또한 Azure Migrate: 서버 마이그레이션 도구를 사용 하 여 VMware 환경에서 실행 중인 서버 마이그레이션을 시작 하 여 에이전트 없는 서버 복제를 오케스트레이션 할 수 있습니다.| Azure Migrate: 검색 및 평가 도구를 사용 하 여 어플라이언스에서 수집한 메타 데이터에서 평가를 만들 수 있습니다. | Azure Migrate: 검색 및 평가 도구를 사용 하 여 어플라이언스에서 수집한 메타 데이터에서 평가를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

어플라이언스 지원 매트릭스를 [검토](migrate-appliance.md) 합니다.