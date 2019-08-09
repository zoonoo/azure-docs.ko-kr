---
title: Azure Migrate 정보 | Microsoft Azure
description: Azure Migrate 서비스의 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227821"
---
# <a name="about-azure-migrate"></a>Azure Migrate 정보

이 문서에서는 Azure Migrate에 대한 빠른 개요를 제공합니다.

Azure Migrate를 사용하면 Azure로 쉽게 마이그레이션할 수 있습니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. 다음과 같은 것을 제공합니다.

- **통합 마이그레이션 플랫폼**: 단일 포털을 사용하여 Azure로의 마이그레이션 과정을 시작, 실행 및 추적합니다.
- **도구 범위**: Azure Migrate는 기본 도구를 제공하고, ISV 도구 뿐만 아니라 다른 Azure 서비스와 통합합니다. 조직의 요구 사항에 따라 적절한 평가 및 마이그레이션 도구를 선택합니다. 
- **Azure Migrate 서버 평가**: 서버 평가 도구를 사용하여 Azure로의 마이그레이션에 대해 온-프레미스 VMware VM 및 Hyper-V VM을 평가합니다.
- **Azure Migrate 서버 마이그레이션**: 서버 마이그레이션 도구를 사용하여 온-프레미스 VMware VM, Hyper-V VM, 클라우드 VM 및 물리적 서버를 Azure로 마이그레이션합니다.
- **Azure Migrate 데이터베이스 평가**: Azure로의 마이그레이션에 대해 온-프레미스 데이터베이스를 평가합니다.
- **Azure Migrate 데이터베이스 마이그레이션**: Azure로 온-프레미스 데이터베이스를 마이그레이션합니다.


## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스에는 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**: 이 버전을 사용하여 Azure Migrate 프로젝트를 만들고, 온-프레미스 컴퓨터를 검색하고, 평가 및 마이그레이션을 오케스트레이션합니다. 이 버전의 새로운 기능에 대해 [자세히 알아보세요](whats-new.md).
- **이전 버전**: 이전 버전의 Azure Migrate를 사용하고 있었다면(온-프레미스 VMware VM의 평가만 지원됨) 이제 현재 버전을 사용해야 합니다. 이전 버전을 사용해서는 Azure Migrate 프로젝트를 더 이상 만들 수 없으며 새 검색을 수행하지 않는 것이 좋습니다. 기존 프로젝트에 액세스하려면 Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다. Azure Migrate 대시보드에는 이전 Azure Migrate 프로젝트에 액세스할 수 있는 알림 및 링크가 있습니다.

## <a name="isv-integration"></a>ISV 통합

Azure Migrate는 네이티브 Azure 도구 외에도, 다양한 ISV 제품에 연결됩니다. 필요한 도구를 식별하고 Azure Migrate 프로젝트에 추가합니다. Azure 및 ISV 도구를 통해 Azure Migrate 프로젝트의 중앙에서 마이그레이션 과정을 추적할 수 있습니다.

**ISV** | **기능**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | 평가
[Device 42](https://docs.device42.com/) | 평가
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 평가
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 평가
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 평가 및 마이그레이션
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 마이그레이션

### <a name="selecting-an-isv-tool"></a>ISV 도구 선택

ISV 도구를 Azure Migrate 프로젝트에 추가한 후에는 ISV 정책에 따라 라이선스를 얻거나 평가판에 등록하여 도구를 시작합니다. 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구 지침 및 설명서에 따라 도구를 Azure Migrate에 연결합니다.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 서버 평가

Azure Migrate 서버 평가 도구는 Azure로의 마이그레이션에 대해 온-프레미스 VMware VM 및 Hyper-V VM을 검색 및 평가합니다. 이 도구는 다음을 식별하는 데 도움이 됩니다.

- **Azure 준비 상태:** 온-프레미스 컴퓨터가 Azure로 마이그레이션할 준비가 되었는지 여부를 평가합니다.
- **Azure 크기 조정:** 마이그레이션 후의 Azure VM 크기를 예측합니다.
- **Azure 비용 예측:** Azure에서 온-프레미스 서버를 실행하기 위한 비용을 예측합니다.
- **종속성 시각화:** 서버 간 종속성 및 종속 서버를 Azure로 이동하는 가장 좋은 방법을 식별합니다. 

서버 평가는 온-프레미스에 배포하고 서버 평가에 등록하는 경량 어플라이언스를 사용합니다.

- 이 어플라이언스는 온-프레미스 컴퓨터를 검색하고, 서버 평가에 연결하고, 메타데이터 및 성능 관련 데이터를 Azure Migrate에 지속적으로 보냅니다.
- 검색은 에이전트 없이 수행됩니다. 검색된 VM에는 아무 것도 설치할 필요가 없습니다.
- 머신을 검색한 후에는 일반적으로 함께 마이그레이션하려는 VM으로 구성되는 그룹에 수집합니다.
- 그룹에 대한 평가를 만듭니다. 그런 다음, 평가를 분석하여 마이그레이션 전략을 파악할 수 있습니다.

## <a name="azure-migrate-server-migration"></a>Azure Migrate 서버 마이그레이션

Azure Migrate 서버 마이그레이션을 사용하면 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버, 기타 가상화된 머신 및 퍼블릭 클라우드 VM을 Azure로 손쉽게 마이그레이션할 수 있습니다. 평가 후에 또는 평가 없이 머신을 마이그레이션할 수 있습니다. 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate 데이터베이스 평가

Azure Migrate는 DMA(Data Migration Assistant)에 연결하여 Azure SQL DB, Azure SQL Managed Instance 또는 SQL Server를 실행하는 Azure VM으로의 마이그레이션을 위해 온-프레미스 SQL Server 데이터베이스를 평가합니다. DMA는 마이그레이션의 잠재적인 차단 문제에 대한 정보를 제공합니다. 지원되지 않는 기능과 마이그레이션 후에 활용할 수 있는 새로운 기능을 식별하고, 데이터베이스 마이그레이션의 올바른 경로를 식별할 수 있도록 합니다. [자세히 알아보기](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Azure Migrate 데이터베이스 마이그레이션

Azure Migrate는 DMS(Azure Database Migration Service)와 연결하여 온-프레미스 데이터베이스를 Azure로 마이그레이션합니다. DMS를 사용하여 온-프레미스 데이터베이스를 SQL, Azure SQL DB 및 Azure SQL Managed Instance를 실행하는 Azure VM으로 마이그레이션합니다. [자세히 알아보기](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>웹앱 평가 및 마이그레이션

Azure Migrate 허브에서 온-프레미스 웹앱을 평가하고 Azure로 마이그레이션할 수 있습니다.

- **온라인으로 웹앱 평가**: Azure App Service Migration Assistant를 사용하여 Azure App Service으로 마이그레이션하기 위한 온-프레미스 웹 사이트를 평가합니다.
- **웹앱 마이그레이션**: Azure App Service Migration Assistant를 사용하여 .NET 및 PHP 웹앱을 Azure로 마이그레이션합니다.

[자세한 정보](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>오프라인 데이터 마이그레이션

Data Box 오프라인 제품군을 사용하여 대량의 데이터를 Azure로 이동할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>다음 단계

- [VMware VM](tutorial-assess-vmware.md) 및 [Hyper-V VM](tutorial-assess-hyper-v.md)을 평가하는 자습서를 사용해 보세요.
- Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).
- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).
