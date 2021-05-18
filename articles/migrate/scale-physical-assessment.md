---
title: Azure Migrate를 사용하여 Azure로 마이그레이션하기 위한 대량의 물리적 서버 평가 | Microsoft Docs
description: Azure Migrate 서비스를 사용하여 Azure로 마이그레이션할 대량의 물리적 서버를 평가하는 방법을 설명합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/19/2020
ms.openlocfilehash: 232475c50ab56fe6fb7a39a3497a8de3947fe851
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780313"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Azure로 마이그레이션할 대량의 물리적 서버 평가

이 문서에서는 Azure Migrate Discovery와 평가 도구를 사용하여 Azure로 마이그레이션할 대량의 물리적 온-프레미스 서버를 평가하는 방법을 설명합니다.

[Azure Migrate](migrate-services-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다. 


이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 대규모 평가 계획.
> * Azure 사용 권한을 구성하고 평가용 물리적 서버를 준비합니다.
> * Azure Migrate 프로젝트를 만들고, 평가를 만듭니다.
> * 마이그레이션을 계획할 때 평가를 검토합니다.


> [!NOTE]
> 대규모 평가를 하기 전에 2대의 서버를 평가하기 위해 개념 증명을 사용해 보려는 경우 [자습서 시리즈](./tutorial-discover-physical.md)를 따르세요.

## <a name="plan-for-assessment"></a>평가 계획

대량의 물리적 서버에 대한 평가를 계획할 때 고려해야 하는 사항이 몇 가지 있습니다.

- **Azure Migrate 프로젝트 계획**: Azure Migrate 프로젝트를 배포하는 방법을 파악합니다. 예를 들어 데이터 센터가 다른 지리에 있거나 검색, 평가 또는 마이그레이션 관련 메타데이터를 다른 지리에 저장해야 하는 경우 여러 프로젝트가 필요할 수 있습니다.
- **계획 어플라이언스**: Azure Migrate은 Windows 서버에 배포된 온-프레미스 Azure Migrate 어플라이언스를 사용하여 평가 및 마이그레이션할 서버를 지속적으로 발견합니다. 어플라이언스는 서버, 디스크 또는 네트워크 어댑터를 추가하는 등의 환경 변화를 모니터링합니다. 또한 그에 대한 메타데이터 및 성능 데이터를 Azure로 보냅니다. 배포할 어플라이언스 수를 파악해야 합니다.


## <a name="planning-limits"></a>한도 계획
 
계획을 위해 이 표에 요약된 한도를 사용합니다.

**계획** | **제한**
--- | --- 
**Azure Migrate 프로젝트** | 한 프로젝트에서 최대 35,000대까지 서버를 평가합니다.
**Azure Migrate 어플라이언스** | 어플라이언스는 최대 1,000개 서버를 발견할 수 있습니다.<br/> 어플라이언스는 단일 Azure Migrate 프로젝트에만 연결할 수 있습니다.<br/> 어플라이언스는 몇 개든 단일 Azure Migrate 프로젝트에 연결할 수 있습니다. <br/><br/> 
**그룹** | 단일 그룹에 최대 35,000개 서버를 추가할 수 있습니다.
**Azure Migrate 평가** | 단일 평가에서 최대 35,000대의 서버를 평가할 수 있습니다.


## <a name="other-planning-considerations"></a>기타 계획 고려 사항

- 어플라이언스에서 검색을 시작하려면 각 물리적 서버를 선택해야 합니다. 

## <a name="prepare-for-assessment"></a>평가 준비

검색 및 평가 도구를 위한 Azure 및 물리적 서버 준비:  

1. [물리적 서버 지원 요구 사항 및 제한 사항](migrate-support-matrix-physical.md)을 확인합니다.
2. Azure Migrate에서 상호 작용할 Azure 계정에 대한 권한을 설정합니다.
3. 물리적 서버를 준비합니다.

[이 자습서](./tutorial-discover-physical.md)의 지침에 따라 이러한 설정을 구성합니다.

## <a name="create-a-project"></a>프로젝트 만들기

계획 요구 사항에 따라 다음을 수행합니다.

1. Azure Migrate 프로젝트를 만듭니다.
2. 프로젝트에 Azure Migrate Discovery와 평가 도구를 추가합니다.

[자세히 알아보기](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>평가를 만들고 검토합니다

1. 물리적 서버에 대한 평가를 만듭니다.
1. 마이그레이션 계획 준비에 대한 평가를 검토합니다.

평가 만들기 및 검토에 대해 [자세한 정보](tutorial-assess-physical.md)를 알아보세요.
    

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 작업을 수행합니다.
 
> [!div class="checklist"] 
> * 물리적 서버에 대한 Azure Migrate 평가를 확장할 계획입니다.
> * Azure 및 물리적 서버 평가를 준비합니다.
> * Azure Migrate 프로젝트를 만들고 평가를 실행했습니다.
> * 마이그레이션 준비 과정에서 평가를 검토했습니다.

이제 평가를 계산하는 방법 및 [평가를 수정](how-to-modify-assessment.md)하는 [방법을 알아봅니다](concepts-assessment-calculation.md).