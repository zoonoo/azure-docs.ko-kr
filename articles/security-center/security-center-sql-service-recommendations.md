---
title: 데이터 & 스토리지 권장 사항 - Azure 보안 센터
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552868"
---
# <a name="protect-azure-data-and-storage-services"></a>Azure 데이터 및 저장소 서비스 보호
Azure Security Center에서 잠재적인 보안 취약점을 식별하면 리소스를 강화하고 보호하는 데 필요한 컨트롤을 구성하는 프로세스를 안내하는 권장 사항을 만듭니다.

이 문서에서는 보안 센터의 리소스 보안 섹션의 데이터 보안 **페이지를** 설명합니다.

이 페이지에 표시될 수 있는 권장 사항의 전체 목록은 [데이터 및 저장소 권장 사항을](recommendations-reference.md#recs-datastorage)참조하십시오.


## <a name="view-your-data-security-information"></a>데이터 보안 정보 보기

1. 리소스 **보안 위생** 섹션에서 **데이터 및 저장소 리소스를**클릭합니다.

    ![데이터 & 스토리지 리소스](./media/security-center-monitoring/click-data.png)

    **데이터 보안** 페이지가 데이터 리소스에 대한 권장 사항과 함께 열립니다.

    [![데이터 리소스](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    이 페이지에서 다음을 수행할 수 있습니다.

    * 수정할 모든 데이터 리소스 권장 사항을 나열하는 **개요** 탭을 클릭합니다. 
    * 각 탭을 클릭하고 리소스 유형별로 권장 사항을 봅니다.

    > [!NOTE]
    > 저장소 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 암호화를](../storage/common/storage-service-encryption.md)참조하십시오.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>데이터 리소스에 대한 권장 사항 수정

1. 리소스 탭에서 리소스를 클릭합니다. 수정할 권장 사항 나열이 열리는 정보 페이지입니다.

    ![리소스 정보](./media/security-center-monitoring/sql-recommendations.png)

2. 권장 사항을 클릭합니다. 권장 사항 페이지가 열리고 권장 사항을 구현하기 위한 **수정 단계를** 표시합니다.

   ![수정 단계](./media/security-center-monitoring/remediate1.png)

3. **작업 수행을 클릭합니다.** 리소스 설정 페이지가 나타납니다.

    ![권장 사항 사용](./media/security-center-monitoring/remediate2.png)

4. 수정 **단계를** 수행하고 **저장을**클릭합니다.


## <a name="next-steps"></a>다음 단계

다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Azure 보안 센터의 보안 권장 사항에 대한 전체 참조 목록](recommendations-reference.md)
* [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)