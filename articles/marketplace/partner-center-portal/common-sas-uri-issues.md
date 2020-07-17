---
title: 일반적인 SAS URI 문제 및 수정 - Azure Marketplace
description: 공유 액세스 서명을 사용할 때 발생하는 일반적인 문제 및 권장되는 해결 방법입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: anbene
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: 95ce37d92adc3d09c5a09944b094df7971831198
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110728"
---
# <a name="common-sas-uri-issues-and-fixes"></a>일반적인 SAS URI 문제 및 수정

다음은 권장되는 해결 방법과 함께 공유 액세스 서명(업로드된 솔루션용 VHD를 식별하고 공유하는 데 사용됨)을 사용할 때 발생하는 몇 가지 일반적인 문제입니다.

| **문제점** | **오류 메시지** | **해결** |
| --------- | ------------------- | ------- |
| *이미지 복사 중 오류* |  |  |
| "?"가 SAS URI에 없습니다 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 권장되는 도구를 사용하여 SAS URI를 업데이트합니다. |
| SAS URI에 없는 "st" 및 "se" 매개 변수 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 적절한 **시작 날짜**와 **종료 날짜** 값으로 업데이트합니다. |
| SAS URI에 없는 "sp=rl" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 `Read` 및 `List`로 설정된 권한으로 업데이트합니다. |
| SAS URI에는 VHD 이름에 공백이 있습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 업데이트하여 공백을 제거합니다. |
| SAS URI 권한 부여 오류 | `Failure: Copying Images. Not able to download blob due to authorization error.` | SAS URI 형식을 검토하고 수정합니다. 필요한 경우 다시 생성합니다. |
| SAS URI "st" 및 "se" 매개 변수에 전체 날짜/시간 사양이 없습니다. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI **시작 날짜** 및 **종료 날짜** 매개 변수(`st` 및 `se` 하위 문자열)는 `11-02-2017T00:00:00Z`와 같이 전체 날짜/시간 형식이어야 합니다. 단축 버전이 잘못되었습니다(Azure CLI의 일부 명령은 기본적으로 약식 값을 생성할 수 있음). |
|  |  |  |

자세한 내용은 [SAS(공유 액세스 서명) 사용](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)을 참조하세요.
