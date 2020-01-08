---
title: 포함 파일
description: 포함 파일
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659878"
---
>[!IMPORTANT]
>직접 만든 리소스는 다른 Azure Machine Learning 자습서 및 방법 문서에 대한 필수 조건으로 사용할 수 있습니다.

### <a name="delete-everything"></a>모든 항목 삭제

사용자가 만든 항목을 사용하지 않으려면 요금이 발생하지 않도록 전체 리소스 그룹을 삭제하세요.

1. Azure Portal의 창 왼쪽에서 **리소스 그룹**을 선택합니다.
 
   ![Azure Portal에서 리소스 그룹 삭제](./media/aml-ui-cleanup/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

리소스 그룹을 삭제하면 디자이너에서 만든 모든 리소스도 삭제됩니다. 

### <a name="delete-individual-assets"></a>개별 자산 삭제

실험을 만든 디자이너에서 개별 자산을 선택한 다음, **삭제** 단추를 선택하여 자산을 삭제합니다.

여기에서 만든 컴퓨팅 대상은 사용하지 않을 경우 제로 노드로 *자동으로 크기 조정*됩니다. 이 작업은 요금을 최소화하기 위해 수행됩니다. 컴퓨팅 대상을 삭제하려는 경우 다음 단계를 수행합니다.

![자산 삭제](./media/aml-ui-cleanup/delete-asset.png)

각 데이터 세트를 선택하고 **등록 취소**를 선택하여 작업 영역에서 데이터 세트를 등록 취소할 수 있습니다.

![데이터 세트 등록 취소](./media/aml-ui-cleanup/unregister-dataset1225.png)

데이터 세트를 삭제하려면 Azure Portal 또는 Azure Storage Explorer를 사용하여 스토리지 계정으로 이동하여 해당 자산을 수동으로 삭제합니다.


