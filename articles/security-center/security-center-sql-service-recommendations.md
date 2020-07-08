---
title: 데이터 & 저장소 권장 사항-Azure Security Center
description: 이 문서에서는 데이터 및 Azure SQL 서비스를 보호하고 보안 정책을 준수하는 데 도움이 되는 Azure Security Center의 권장 사항에 대해 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552868"
---
# <a name="protect-azure-data-and-storage-services"></a>Azure 데이터 및 저장소 서비스 보호
Azure Security Center에서 잠재적인 보안 취약점을 식별 하는 경우 리소스를 강화 하 고 보호 하는 데 필요한 컨트롤을 구성 하는 과정을 안내 하는 권장 사항을 만듭니다.

이 문서에서는 Security Center 리소스 보안 섹션의 **데이터 보안 페이지** 에 대해 설명 합니다.

이 페이지에 표시 될 수 있는 권장 사항의 전체 목록은 [데이터 및 저장소 권장 사항](recommendations-reference.md#recs-datastorage)을 참조 하세요.


## <a name="view-your-data-security-information"></a>데이터 보안 정보 보기

1. **리소스 보안 예방** 섹션에서 **데이터 및 저장소 리소스**를 클릭 합니다.

    ![저장소 리소스 & 데이터](./media/security-center-monitoring/click-data.png)

    데이터 리소스에 대 한 권장 사항이 포함 된 **데이터 보안** 페이지가 열립니다.

    [![데이터 리소스](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    이 페이지에서 다음을 수행할 수 있습니다.

    * **개요** 탭을 클릭 하면 수정할 모든 데이터 리소스 권장 사항이 나열 됩니다. 
    * 각 탭을 클릭 하 고 리소스 유형별 권장 사항을 봅니다.

    > [!NOTE]
    > 저장소 암호화에 대 한 자세한 내용은 [미사용 데이터에 대 한 암호화 Azure Storage](../storage/common/storage-service-encryption.md)를 참조 하세요.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>데이터 리소스에 대 한 권장 사항 재구성

1. 리소스 탭에서 리소스를 클릭 합니다. 재구성 될 권장 사항이 나열 된 정보 페이지가 열립니다.

    ![리소스 정보](./media/security-center-monitoring/sql-recommendations.png)

2. 권장 사항을 클릭 합니다. 권장 사항 페이지가 열리고 권장 사항을 구현 하는 **수정 단계가** 표시 됩니다.

   ![수정 단계](./media/security-center-monitoring/remediate1.png)

3. **작업 수행**을 클릭 합니다. 리소스 설정 페이지가 나타납니다.

    ![권장 구성 사용](./media/security-center-monitoring/remediate2.png)

4. **수정 단계** 를 수행 하 고 **저장**을 클릭 합니다.


## <a name="next-steps"></a>다음 단계

다른 Azure 리소스 유형에 적용 되는 권장 사항에 대해 자세히 알아보려면 다음 항목을 참조 하세요.

* [Azure Security Center의 보안 권장 사항에 대 한 전체 참조 목록](recommendations-reference.md)
* [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)