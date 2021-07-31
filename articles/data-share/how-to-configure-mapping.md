---
title: Azure Data Share에서 데이터 세트 매핑 구성
description: Azure Data Share를 사용하여 수신된 공유에 대한 데이터 세트 매핑을 구성하는 방법을 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88257844"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure Data Share에서 수신된 공유에 대한 데이터 세트 매핑을 구성하는 방법

이 문서에서는 Azure Data Share를 사용하여 수신된 공유에 대한 데이터 세트 매핑을 구성하는 방법을 설명합니다. 데이터를 수신할 대상 데이터 저장소를 지정하거나 대상 데이터 저장소를 변경해야 하는 경우 데이터 세트 매핑을 구성하는 것이 좋습니다.

## <a name="navigate-to-a-received-data-share"></a>수신된 데이터 공유로 이동

Azure Data Share 서비스에서 수신된 공유로 이동하여 **데이터 세트** 탭을 선택합니다. 

![데이터 세트 매핑](./media/dataset-mapping.png "데이터 세트 매핑") 

대상을 할당하려는 데이터 세트 옆에 있는 상자를 선택합니다. **+ 대상에 매핑** 을 선택하여 새 대상 저장소를 선택합니다. 데이터 세트가 이미 매핑되었으며 대상 데이터 저장소를 변경하려는 경우 먼저 **매핑 해제** 를 선택합니다.

![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

## <a name="select-a-target-store"></a>대상 저장소 선택

데이터를 가져올 대상 데이터 저장소 유형을 선택합니다. 스냅샷 기반 공유의 경우 이미 이전에 매핑한 스토리지 계정에 있는 데이터는 자동으로 새 대상 저장소로 이동되지 않습니다. 내부 공유의 경우 지정된 위치에서 데이터 저장소를 선택합니다. 위치는 데이터 공급자의 원본 데이터 저장소가 있는 Azure 데이터 센터입니다.

![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 스토리지") 

## <a name="select-a-file-format-sql-sources-only"></a>파일 형식 선택(SQL 원본만 해당)

원본 데이터가 SQL 기반 원본에 있으며 파일로 받으려는 경우 수신 형식을 선택할 수 있습니다. 

![형식 선택](./media/sql-file-formats.png "SQL 파일 형식")

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.



