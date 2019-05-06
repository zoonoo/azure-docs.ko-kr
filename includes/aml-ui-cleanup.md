---
title: 포함 파일
description: 포함 파일
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028592"
---
>[!IMPORTANT]
>다른 Azure Machine Learning 서비스 자습서 및 방법 문서에 대 한 필수 조건으로 사용자가 만든 리소스를 사용할 수 있습니다.


### <a name="delete-everything"></a>모든 항목을 삭제합니다

사용자가 만든 값을 사용 하지 않으려는 경우 요금이 발생 하지 않도록 전체 리소스 그룹을 삭제 합니다.

1. Azure portal에서 선택 **리소스 그룹** 창의 왼쪽에 있습니다.
 
   ![Azure Portal에서 리소스 그룹 삭제](./media/aml-ui-cleanup/delete-resources.png)

1. 목록에서 사용자가 만든 리소스 그룹을 선택 합니다.

1. 창의 오른쪽에서 줄임표 단추를 선택 (**...** ).

1. **리소스 그룹 삭제**를 선택합니다.

시각적 인터페이스에서 만든 모든 리소스도 삭제 리소스 그룹을 삭제 합니다.  

### <a name="delete-only-the-compute-target"></a>계산 대상만 삭제

여기에서 만든 계산 대상을 *자동으로 자동 크기 조정* 0 노드로 사용 되는 경우. 이 요금을 최소화 하는 것입니다. 계산 대상을 삭제 하려는 경우 다음이 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 작업 영역을 엽니다.

    ![계산 대상 삭제](./media/aml-ui-cleanup/delete-compute-target.png)

1. 에 **계산** 섹션의 작업 영역에 리소스를 선택 합니다.

1. **삭제**를 선택합니다.

### <a name="delete-individual-assets"></a>개별 자산 삭제

실험을 만든 visual 인터페이스에서 선택 하 고 다음을 선택 하 여 개별 자산을 삭제 합니다 **삭제** 단추입니다.

![실험을 삭제 합니다.](./media/aml-ui-cleanup/delete-experiment.png)
