---
title: 일반적인 SAS URI 문제 및 수정 사항 - Azure 마켓플레이스
description: 공유 액세스 서명으로 작업할 때 일반적인 문제가 발생하고 제안된 해결 방법.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266241"
---
# <a name="common-sas-uri-issues-and-fixes"></a>일반적인 SAS URI 문제 및 수정 사항

> [!IMPORTANT]
> Azure 가상 컴퓨터 의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 [공통 SAS URI 문제의 지침과](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) 클라우드 파트너 포털의 수정 사항을 따라 오퍼를 관리하십시오.

다음은 제안된 해결 방법과 함께 업로드된 VHD를 식별하고 공유하는 데 사용되는 공유 액세스 서명으로 작업할 때 발생하는 일반적인 문제입니다.

| **문제** | **오류 메시지** | **수정** |
| --------- | ------------------- | ------- |
| *이미지 복사 중 오류* |  |  |
| "?" SAS URI에서 찾을 수 없습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 권장 도구를 사용하여 SAS URI를 업데이트합니다. |
| SAS URI에 없는 "st" 및 "se" 매개 변수 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 적절한 **시작 날짜** 및 종료 **날짜** 값으로 업데이트합니다. |
| SAS URI에 없는 "sp=rl" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 권한으로 `Read` 업데이트하고. `List` |
| SAS URI에는 VHD 이름에 공백이 있습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 업데이트하여 공백을 제거합니다. |
| SAS URI 권한 부여 오류 | `Failure: Copying Images. Not able to download blob due to authorization error.` | SAS URI 형식을 검토하고 수정합니다. 필요한 경우 다시 생성합니다. |
| SAS URI "st" 및 "se" 매개 변수에는 전체 날짜 시간 사양이 없습니다. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI 시작 날짜 및`st` 종료 `se` **날짜** 매개 변수(및 하위 문자열)에는 `11-02-2017T00:00:00Z`와 같은 전체 날짜 시간 형식이 있어야 합니다. **End Date** 단축된 버전은 유효하지 않습니다(Azure CLI의 일부 명령은 기본적으로 단축된 값을 생성할 수 있음). |
|  |  |  |

자세한 내용은 [SAS(공유 액세스 서명 사용)를](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)참조하십시오.
