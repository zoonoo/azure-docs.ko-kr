---
title: Azure Migrate의 새로운 기능
description: Azure Migrate 서비스의 새로운 기능 및 최신 업데이트에 대해 알아봅니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 63b6783a2f36d5bc9e84ce8291e7025b27359b6c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362134"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate의 새로운 기능

[Azure Migrate](migrate-services-overview.md)를 사용하면 온-프레미스 서버, 앱 및 데이터를 검색 및 평가하고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 Azure Migrate의 새로운 기능을 요약합니다.



## <a name="update-november-2019"></a>업데이트(2019년 11월)

Azure Migrate에 여러 가지 새로운 기능이 추가되었습니다.

- **물리적 서버 평가**. 이미 지원되는 물리적 서버 마이그레이션 외에도 온-프레미스 물리적 서버의 평가가 지원됩니다.
- **가져오기 기반 평가**. 이제 CSV 파일로 제공되는 메타데이터 및 성능 데이터를 사용한 컴퓨터 평가가 지원됩니다.
- **응용 프로그램 검색**: 이제 Azure Migrate Azure Migrate 어플라이언스를 사용 하 여 앱, 역할 및 기능의 응용 프로그램 수준 검색을 지원 합니다. 이는 현재 VMware VM에서만 지원되며 검색으로만 제한됩니다(평가는 현재 지원되지 않음). [자세히 알아보기](how-to-discover-applications.md)
- **에이전트 없는 종속성 시각화**: 더 이상 종속성 시각화를 위해 에이전트를 명시적으로 설치할 필요가 없습니다. 이제 에이전트 없이 또는 에이전트 기반이 모두 지원됩니다.
- **가상 데스크톱**: ISV 도구를 사용 하 여 온-프레미스 VDI (가상 데스크톱 인프라)를 평가 하 고 Azure의 Windows 가상 데스크톱으로 마이그레이션합니다.
- **웹 앱**: 웹 앱 평가 및 마이그레이션에 사용 되는 Azure App Service Migration Assistant 이제 Azure Migrate에 통합 됩니다.

Azure Migrate에 새로운 평가 및 마이그레이션 도구가 추가되었습니다.

- **Rackware**: 클라우드 마이그레이션 제공
- **Movere**: 평가를 제공 합니다.

Azure Migrate에서 평가 및 마이그레이션을 위해 도구 및 ISV 제품을 사용하는 방법에 대해 [자세히 알아봅니다](migrate-services-overview.md).

## <a name="release-version-july-2019"></a>릴리스 버전(2019년 7월)

현재 버전의 Azure Migrate는 2019년 7월에 출시되었습니다.

- **현재 버전**:이 버전을 사용 하 여 Azure Migrate 프로젝트를 만들고, 온-프레미스 컴퓨터를 검색 하 고, 평가 및 마이그레이션을 오케스트레이션 합니다.
- **이전 버전**: 이전 버전의 Azure Migrate를 사용 하는 고객의 경우 (온-프레미스 VMware vm에 대 한 평가만 지원 됨) 이제 현재 버전을 사용 해야 합니다. 이전 버전에서는 더 이상 새 Azure Migrate 프로젝트를 만들거나 새 검색을 수행할 수 없습니다. 기존 프로젝트에 계속 액세스할 수 있습니다. Azure Portal > **모든 서비스**에서 이 작업을 수행하려면 **Azure Migrate**를 검색합니다. Azure Migrate 알림에는 이전 Azure Migrate 프로젝트에 액세스할 수 있는 링크가 있습니다.


### <a name="azure-migrate-features"></a>Azure Migrate 기능

현재 버전의 Azure Migrate는 다음과 같은 여러 가지 새로운 기능을 제공합니다.


- **통합 마이그레이션 플랫폼**: 이제 향상 된 배포 흐름 및 포털 환경을 통해 Azure에 대 한 마이그레이션 경험을 중앙화, 관리 및 추적 하는 단일 포털을 제공 Azure Migrate 합니다.
- **평가 및 마이그레이션 도구**: Azure Migrate는 기본 도구를 제공 하며, ISV (독립 소프트웨어 공급 업체) 도구를 비롯 하 여 다른 Azure 서비스와 통합 됩니다. ISV 통합에 대해 [자세히 알아보세요](migrate-services-overview.md#isv-integration).
- **Azure Migrate 평가**: Azure Migrate Server 평가 도구를 사용 하 여 Azure로 마이그레이션하기 위한 VMware Vm 및 hyper-v vm을 평가할 수 있습니다. 또한 다른 Azure 서비스 및 ISV 도구를 사용하여 마이그레이션을 평가할 수도 있습니다.
- **Azure Migrate 마이그레이션**: Azure Migrate 서버 마이그레이션 도구를 사용 하 여 온-프레미스 VMware Vm 및 hyper-v Vm을 Azure, 실제 서버, 다른 가상화 된 서버, 사설/공용 클라우드 vm으로 마이그레이션할 수 있습니다. 또한 ISV 도구를 사용하여 Azure로 마이그레이션할 수 있습니다.
- **Azure Migrate 어플라이언스**: 온-프레미스 VMware Vm 및 hyper-v vm의 검색 및 평가를 위해 경량 어플라이언스를 배포 Azure Migrate 합니다.
    - 이 어플라이언스는 Azure Migrate 서버 평가에 사용되고, 에이전트 없는 마이그레이션을 위해 Azure Migrate 서버 마이그레이션에서 사용됩니다.
    - 어플라이언스는 평가 및 마이그레이션을 위해 서버 메타데이터 및 성능 데이터를 지속적으로 검색합니다.  
- **VMWARE vm 마이그레이션**: Azure Migrate 서버 마이그레이션은 온-프레미스 VMware Vm을 Azure로 마이그레이션하기 위한 몇 가지 방법을 제공 합니다.  Azure Migrate 어플라이언스를 사용하는 에이전트 없는 마이그레이션과 복제 어플라이언스를 사용하며 마이그레이션하려는 각 VM에 에이전트를 배포하는 에이전트 기반 마이그레이션이 있습니다. [자세히 알아보기](server-migrate-overview.md)
 - **데이터베이스 평가 및 마이그레이션**: Azure Migrate에서 azure Database Migration Assistant를 사용 하 여 azure로 마이그레이션하기 위한 온-프레미스 데이터베이스를 평가할 수 있습니다. Azure Database Migration Service를 사용하여 데이터베이스를 마이그레이션할 수 있습니다.
- **웹 앱 마이그레이션**: Azure App Service와 함께 공용 끝점 URL을 사용 하 여 웹 앱을 평가할 수 있습니다. 내부 .NET 앱의 마이그레이션을 위해 App Service Migration Assistant를 다운로드하여 실행할 수 있습니다.
- **Data Box**: Azure Migrate의 Azure Data Box를 사용 하 여 대량의 오프 라인 데이터를 Azure로 가져옵니다.


## <a name="next-steps"></a>다음 단계

- Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).
- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).
- [VMware VM](tutorial-assess-vmware.md) 및 [Hyper-V VM](tutorial-assess-hyper-v.md)을 평가하는 자습서를 사용해 보세요.
