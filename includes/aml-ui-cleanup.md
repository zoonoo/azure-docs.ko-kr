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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182620"
---
>[!IMPORTANT]
>직접 만든 리소스는 다른 Azure Machine Learning Service 자습서 및 방법 문서에 대한 필수 조건으로 사용할 수 있습니다.


### <a name="delete-everything"></a>모든 항목 삭제

자신이 만든 항목을 사용하지 않으려는 경우 요금이 발생하지 않도록 전체 리소스 그룹을 삭제하세요.

1. Azure Portal의 창 왼쪽에서 **리소스 그룹**을 선택합니다.
 
   ![Azure Portal에서 리소스 그룹 삭제](./media/aml-ui-cleanup/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. 창의 오른쪽에서 줄임표 단추( **...** )를 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

리소스 그룹을 삭제하면 시각적 인터페이스에서 만든 모든 리소스도 삭제됩니다.  

### <a name="delete-only-the-compute-target"></a>컴퓨팅 대상만 삭제

여기에서 만든 컴퓨팅 대상은 사용하지 않을 경우 제로 노드로 *자동으로 크기 조정*됩니다. 이러한 기능은 요금을 최소화하기 위한 것입니다. 컴퓨팅 대상을 삭제하려는 경우 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 작업 영역을 엽니다.

    ![컴퓨팅 대상 삭제](./media/aml-ui-cleanup/delete-compute-target.png)

1. 작업 영역의 **컴퓨팅** 섹션에서 리소스를 선택합니다.

1. **삭제**를 선택합니다.

### <a name="delete-individual-assets"></a>개별 자산 삭제

실험을 만든 시각적 인터페이스에서 개별 자산을 선택한 후 **삭제** 단추를 선택하여 자산을 삭제합니다.

![실험 삭제기](./media/aml-ui-cleanup/delete-experiment.png)
