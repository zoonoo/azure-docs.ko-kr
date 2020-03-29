---
title: Azure 데이터 공유에서 데이터 집합 매핑 구성
description: Azure 데이터 공유를 사용하여 수신된 공유에 대한 데이터 집합 매핑을 구성하는 방법을 알아봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964246"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure 데이터 공유에서 수신된 공유에 대 한 데이터 집합 매핑을 구성 하는 방법

이 문서에서는 Azure 데이터 공유를 사용하여 수신된 공유에 대한 데이터 집합 매핑을 구성하는 방법을 설명합니다. 데이터 공유 초대를 수락했지만 "나중에 수락 및 구성"을 선택했거나 데이터가 제자리에서 공유되는 경우 이 작업을 수행할 수 있습니다. 사용자와 공유되는 데이터의 대상을 변경해야 하거나 SQL Server로 데이터를 수신하려는 경우 데이터 집합 매핑을 구성할 수 있습니다. 

## <a name="navigate-to-a-received-data-share"></a>수신된 데이터 공유로 이동

Azure 데이터 공유 서비스에서 수신된 공유로 이동하여 **세부 정보** 탭을 선택합니다. 

![데이터 집합 매핑](./media/dataset-mapping.png "데이터 집합 매핑") 

대상을 할당할 데이터 집합 옆의 확인란을 선택합니다. 맵 **해제를** 선택하여 기존 매핑의 매핑을 해제합니다. **+ 지도를 선택하여 대상을** 지정하여 새 대상 저장소를 선택합니다. 

![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

## <a name="select-a-new-target-store"></a>새 대상 저장소 선택

데이터를 착륙할 대상 데이터 유형을 선택합니다. 스냅숏 기반 공유의 경우 이전에 매핑된 저장소 계정에 이미 있는 데이터는 새 대상 저장소로 자동으로 이동되지 않습니다. 위치 공유의 경우 지정된 위치에서 데이터 저장소를 선택합니다. 위치는 데이터 공급자의 원본 데이터 저장소가 있는 Azure 데이터 센터입니다.

![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 스토리지") 

## <a name="select-a-file-format-sql-sources-only"></a>파일 형식 선택(SQL 원본에만)

원본 데이터가 SQL 기반 원본의 데이터인 경우 수신되는 형식을 선택할 수 있습니다. 

![형식 선택](./media/sql-file-formats.png "SQL 파일 형식")

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.



