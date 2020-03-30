---
title: Azure Migrate의 새로운 기능
description: Azure Migrate 서비스의 새로운 기능 및 최신 업데이트에 대해 알아봅니다.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: 9767f3ea31b57d23c8a6772ff5eb6500f7550802
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127583"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate의 새로운 기능

[Azure Migrate](migrate-services-overview.md)를 사용하면 온-프레미스 서버, 앱 및 데이터를 검색 및 평가하고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 Azure Migrate의 새로운 릴리스와 기능이 요약되어 있습니다.

## <a name="update-march-2020"></a>업데이트(2020년 3월)

이제 [Azure Migrate 어플라이언스](migrate-appliance.md)를 설정하는 스크립트 기반 설치를 사용할 수 있습니다.

- 스크립트 기반 설치는 어플라이언스의 .OVA(VMware)/VHD(Hyper-V) 설치의 대안입니다.
- Windows Server 2016을 실행하는 기존 머신에서 VMware/Hyper-V용 어플라이언스를 설정하는 데 사용할 수 있는 PowerShell 설치 관리자 스크립트를 제공합니다.

## <a name="update-november-2019"></a>업데이트(2019년 11월)

Azure Migrate에 여러 가지 새로운 기능이 추가되었습니다.

- **물리적 서버 평가**. 이미 지원되는 물리적 서버 마이그레이션 외에도 온-프레미스 물리적 서버의 평가가 지원됩니다.
- **가져오기 기반 평가**. 이제 CSV 파일로 제공되는 메타데이터 및 성능 데이터를 사용한 컴퓨터 평가가 지원됩니다.
- **애플리케이션 검색**: Azure Migrate는 이제 Azure Migrate 어플라이언스를 사용하여 애플리케이션 수준의 앱, 역할 및 기능 검색을 지원합니다. 이는 현재 VMware VM에서만 지원되며 검색으로만 제한됩니다(평가는 현재 지원되지 않음). [자세히 알아보기](how-to-discover-applications.md)
- **에이전트 없는 종속성 시각화**: 종속성 시각화를 위해 더 이상 에이전트를 명시적으로 설치할 필요가 없습니다. 이제 에이전트 없이 또는 에이전트 기반이 모두 지원됩니다.
- **가상 데스크톱**: ISV 도구를 사용하여 온-프레미스 VDI(가상 데스크톱 인프라)를 평가하고 Azure의 Windows Virtual Desktop으로 마이그레이션합니다.
- **웹앱**: 이제 웹앱을 평가하고 마이그레이션하는 데 사용되는 Azure App Service Migration Assistant가 Azure Migrate에 통합되었습니다.

Azure Migrate에 새로운 평가 및 마이그레이션 도구가 추가되었습니다.

- **Rackware**: 제품 클라우드 마이그레이션.
- **Movere**: 제품 평가.

Azure Migrate에서 평가 및 마이그레이션을 위해 도구 및 ISV 제품을 사용하는 방법에 대해 [자세히 알아봅니다](migrate-services-overview.md).

## <a name="azure-migrate-current-version"></a>Azure Migrate 현재 버전

현재 버전의 Azure Migrate(2019년 7월 릴리스됨)는 다음과 같은 여러 가지 새로운 기능을 제공합니다.

- **통합 마이그레이션 플랫폼**: 이제 Azure Migrate는 향상된 배포 흐름과 포털 환경을 통해 Azure에 대한 마이그레이션 과정을 중앙에서 처리하고, 관리 및 추적하는 단일 포털을 제공합니다.
- **평가 및 마이그레이션 도구**: Azure Migrate는 기본 도구를 제공하고, ISV(독립 소프트웨어 공급업체) 도구 뿐만 아니라 다른 Azure 서비스와 연결됩니다. ISV 통합에 대해 [자세히 알아보세요](migrate-services-overview.md#isv-integration).
- **Azure Migrate 평가**: Azure Migrate Server 평가 도구를 사용하여 Azure로의 마이그레이션에 대해 VMware VM 및 Hyper-V VM을 평가할 수 있습니다. 또한 다른 Azure 서비스 및 ISV 도구를 사용하여 마이그레이션을 평가할 수도 있습니다.
- **Azure Migrate 마이그레이션**: Azure Migrate 서버 마이그레이션 도구를 사용하여 온-프레미스 VMware VM 및 Hyper-V VM을 실제 서버, 다른 가상화된 서버, 프라이빗/퍼블릭 클라우드 VM 뿐만 아니라 Azure로 마이그레이션할 수 있습니다. 또한 ISV 도구를 사용하여 Azure로 마이그레이션할 수 있습니다.
- **Azure Migrate 어플라이언스**: Azure Migrate는 온-프레미스 VMware VM 및 Hyper-V VM의 검색 및 평가를 위해 경량 어플라이언스를 배포합니다.
    - 이 어플라이언스는 Azure Migrate 서버 평가에 사용되고, 에이전트 없는 마이그레이션을 위해 Azure Migrate 서버 마이그레이션에서 사용됩니다.
    - 어플라이언스는 평가 및 마이그레이션을 위해 서버 메타데이터 및 성능 데이터를 지속적으로 검색합니다.  
- **VMware VM 마이그레이션**:  Azure Migrate 서버 마이그레이션은 온-프레미스 VMware VM을 Azure로 마이그레이션하기 위한 몇 가지 방법을 제공합니다.  Azure Migrate 어플라이언스를 사용하는 에이전트 없는 마이그레이션과 복제 어플라이언스를 사용하며 마이그레이션하려는 각 VM에 에이전트를 배포하는 에이전트 기반 마이그레이션이 있습니다. [자세히 알아보기](server-migrate-overview.md)
 - **데이터베이스 평가 및 마이그레이션**: Azure Migrate에서 Azure Database Migration Assistant를 사용하여 Azure로 마이그레이션하기 위한 온-프레미스 데이터베이스를 평가할 수 있습니다. Azure Database Migration Service를 사용하여 데이터베이스를 마이그레이션할 수 있습니다.
- **웹앱 마이그레이션**: Azure App Service와 함께 퍼블릭 엔드포인트 URL을 사용하여 웹앱을 평가할 수 있습니다. 내부 .NET 앱의 마이그레이션을 위해 App Service Migration Assistant를 다운로드하여 실행할 수 있습니다.
- **Data Box**: Azure Migrate에서 Azure Data Box를 사용하여 많은 양의 오프라인 데이터를 Azure로 가져옵니다.

## <a name="azure-migrate-previous-version"></a>Azure Migrate 이전 버전

이전 버전의 Azure Migrate를 사용하고 있었다면(온-프레미스 VMware VM의 평가만 지원됨) 이제 현재 버전을 사용해야 합니다. 이전 버전에서는 더 이상 새 Azure Migrate 프로젝트를 만들거나 새 검색을 수행할 수 없습니다. 기존 프로젝트에 계속 액세스할 수 있습니다. Azure Portal > **모든 서비스**에서 이 작업을 수행하려면 **Azure Migrate**를 검색합니다. Azure Migrate 알림에는 이전 Azure Migrate 프로젝트에 액세스할 수 있는 링크가 있습니다.



## <a name="next-steps"></a>다음 단계

- Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).
- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).
- [VMware VM](tutorial-assess-vmware.md) 및 [Hyper-V VM](tutorial-assess-hyper-v.md)을 평가하는 자습서를 사용해 보세요.
