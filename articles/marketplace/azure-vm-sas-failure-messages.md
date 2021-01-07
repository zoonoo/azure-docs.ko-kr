---
title: 가상 컴퓨터 SAS 오류 메시지-Azure Marketplace
description: SAS (공유 액세스 서명)를 사용 하는 경우 질문과 대답입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126841"
---
# <a name="virtual-machine-sas-failure-messages"></a>가상 컴퓨터 SAS 오류 메시지

다음은 권장되는 해결 방법과 함께 공유 액세스 서명(업로드된 솔루션용 VHD를 식별하고 공유하는 데 사용됨)을 사용할 때 발생하는 몇 가지 일반적인 문제입니다.

| 문제 | 오류 메시지 | Fix |
| --------- | ------------------- | ------- |
| *이미지 복사 중 오류* |  |  |
| "?"가 SAS URI에 없습니다 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 권장되는 도구를 사용하여 SAS URI를 업데이트합니다. |
| SAS URI에 없는 "st" 및 "se" 매개 변수 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 적절한 **시작 날짜** 와 **종료 날짜** 값으로 업데이트합니다. |
| SAS URI에 없는 "sp=rl" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 `Read` 및 `List`로 설정된 권한으로 업데이트합니다. |
| SAS URI에는 VHD 이름에 공백이 있습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 업데이트하여 공백을 제거합니다. |
| SAS URI 권한 부여 오류 | `Failure: Copying Images. Not able to download blob due to authorization error.` | SAS URI 형식을 검토하고 수정합니다. 필요한 경우 다시 생성합니다. |
| SAS URI "st" 및 "se" 매개 변수에 전체 날짜/시간 사양이 없습니다. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI **시작 날짜** 및 **종료 날짜** 매개 변수(`st` 및 `se` 하위 문자열)는 `11-02-2017T00:00:00Z`와 같이 전체 날짜/시간 형식이어야 합니다. 단축 버전이 잘못되었습니다(Azure CLI의 일부 명령은 기본적으로 약식 값을 생성할 수 있음). |
|  |  |  |

자세한 내용은 [SAS(공유 액세스 서명) 사용](../storage/common/storage-sas-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [SAS URI 생성](azure-vm-get-sas-uri.md)