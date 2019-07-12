---
title: Azure Migrate에서 처음으로 평가/마이그레이션 도구에 추가 | Microsoft Docs
description: Azure Migrate 프로젝트를 만들고 평가/마이그레이션 도구에 추가 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812026"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>처음으로 평가/마이그레이션 도구에 추가

이 문서에서는 평가 또는 마이그레이션 도구를 추가 하는 방법에 설명 합니다는 [Azure Migrate](migrate-overview.md) 처음에 대 한 프로젝트입니다.  
Azure Migrate 검색, 평가 및 온-프레미스 앱 및 워크 로드 및 사설/공용 클라우드 Vm에서 Azure로 마이그레이션을 추적 하는 중앙 허브를 제공 합니다. 다른 공급 업체, 독립 소프트웨어 공급 업체 (ISV) 뿐만 아니라 평가 및 마이그레이션을 위한 Azure Migrate 도구를 제공 하는 허브 [제품](migrate-services-overview.md#isv-integration) 합니다. 

## <a name="create-a-project-and-add-a-tool"></a>프로젝트를 만들고 도구 추가

Azure 구독에 새 Azure Migrate 프로젝트를 설정 하 고 도구를 추가 합니다.

- Azure Migrate 프로젝트를 검색, 평가 및 평가 또는 마이그레이션 중인 환경에서 수집 하는 마이그레이션 메타 데이터 저장에 사용 됩니다. 
- 프로젝트에서 검색 된 자산을 추적 하 고 평가 및 마이그레이션 작업을 조정 합니다.

1. Azure portal에서 > **모든 서비스**, 검색할 **Azure Migrate**합니다.
2. 아래 **Services**를 선택 **Azure Migrate**합니다.

    ![Azure Migrate를 설정합니다](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. **개요**, 클릭 **평가 서버를 마이그레이션하고**합니다.
4. 아래 **검색 및 평가 하 고 서버를 마이그레이션하려면**, 클릭 **평가 서버를 마이그레이션하고**합니다.

    ![검색 및 서버를 평가 합니다.](./media/how-to-add-tool-first-time/assess-migrate.png)

1. **검색 및 평가 하 고 서버를 마이그레이션하려면**, 클릭 **도구 추가**합니다.
2. **마이그레이션 프로젝트**, Azure 구독을 선택 하 고 없는 경우 리소스 그룹을 만듭니다.
3. **프로젝트 세부 정보**, 프로젝트 이름과 프로젝트를 만들려고 할 수 있는 지리적 위치를 지정 합니다. 

    ![Azure Migrate 프로젝트 만들기](./media/how-to-add-tool-first-time/migrate-project.png)

    이러한 지역에서 Azure Migrate 프로젝트를 만들 수 있습니다.

    **Geography** | **저장소 위치 지역**
    --- | ---
    아시아 | 동남 아시아 또는 동아시아
    Europe | 남부 유럽 또는 유럽 서 부
    영국 | 영국 남부 또는 영국 서 부
    미국 | 미국 중서부 또는 미국 서 부 2

    프로젝트에 대해 지정된 지리는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다. 실제 마이그레이션에 대 한 모든 대상 지역을 선택할 수 있습니다.

4. 클릭 **다음**, 평가 또는 마이그레이션 도구를 추가 합니다.

    > [!NOTE]
    > 프로젝트를 만들 때 하나 이상의 평가 또는 마이그레이션 도구를 추가 해야 합니다.

5. **선택 평가 도구**, 평가 도구에 추가 합니다. 평가 도구는 필요 하지 않으면, 선택 **지금은 평가 도구를 추가 하지 않고 건너뛰거나** > **다음**합니다. 
2. **선택 마이그레이션 도구**, 필요에 따라 마이그레이션 도구를 추가 합니다. 마이그레이션 도구를 지금 바로 필요 하지 않으면, 선택 **이제 마이그레이션 도구를 추가 하지 않고 건너뛰거나** > **다음**합니다.
3. **검토 도구 추가 +** 설정을 검토 하 고 클릭 **도구 추가**합니다.

프로젝트를 만든 후 서버 및 워크 로드, 데이터베이스 및 웹 앱의 마이그레이션하고 평가 위한 추가 도구를 선택할 수 있습니다.

## <a name="create-additional-projects"></a>추가 프로젝트 만들기

경우에 따라 추가 Azure Migrate 프로젝트를 만드는 해야 합니다. 예를 들어 서로 다른 지역 데이터 센터에 또는 다른 지역에 메타 데이터를 저장 해야 합니다. 다음과 같이 추가 프로젝트를 만듭니다.

1. 현재 Azure Migrate 프로젝트에서 클릭 **서버** 하거나 **데이터베이스**합니다.
2. 오른쪽 위 모퉁이에서 클릭 **변경** 현재 프로젝트 이름 옆에 있습니다.
3. **설정을**를 선택 **새 프로젝트를 만들려면 여기를 클릭 하십시오.** 합니다.
4. 새 프로젝트를 만들고 이전 절차에 설명 된 대로 새 도구를 추가 합니다.

## <a name="next-steps"></a>다음 단계

더 추가 하는 방법을 알아봅니다 [평가](how-to-assess.md) 하 고 [마이그레이션](how-to-migrate.md) 도구입니다. 
