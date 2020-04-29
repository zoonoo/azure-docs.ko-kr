---
title: 일반적인 SAS URI 문제 및 픽스-Azure Marketplace
description: 공유 액세스 서명을 사용할 때 발생 하는 일반적인 문제 및 해결 방법입니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266241"
---
# <a name="common-sas-uri-issues-and-fixes"></a>일반적인 SAS URI 문제 및 픽스

> [!IMPORTANT]
> Azure Virtual Machine 제품의 관리를 Cloud 파트너 포털에서 파트너 센터로 전환 하 고 있습니다. 제품이 마이그레이션될 때까지 Cloud 파트너 포털에 대 한 [일반적인 SAS URI 문제 및 픽스](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) 의 지침에 따라 제품을 관리 하세요.

다음은 권장 해결 방법과 함께 공유 액세스 서명 (솔루션에 대해 업로드 된 Vhd를 식별 및 공유 하는 데 사용 됨)을 사용할 때 발생 하는 일반적인 문제입니다.

| **문제** | **오류 메시지** | **방법을** |
| --------- | ------------------- | ------- |
| *이미지 복사 중 오류* |  |  |
| SAS URI에 "?"가 없습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 권장 도구를 사용 하 여 SAS URI를 업데이트 합니다. |
| SAS URI에 없는 "st" 및 "se" 매개 변수 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 적절 한 **시작 날짜** 및 **종료 날짜** 값을 사용 하 여 SAS URI를 업데이트 합니다. |
| SAS URI에 "sp = rl"이 없습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 및 `Read` `List`로 설정 된 사용 권한으로 SAS URI를 업데이트 합니다. |
| SAS URI의 VHD 이름에 공백이 있습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI를 업데이트 하 여 공백을 제거 합니다. |
| SAS URI 권한 부여 오류 | `Failure: Copying Images. Not able to download blob due to authorization error.` | SAS URI 형식을 검토하고 수정합니다. 필요한 경우 다시 생성합니다. |
| SAS URI "st" 및 "se" 매개 변수는 전체 날짜-시간 지정을 포함 하지 않습니다. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI **시작 날짜** 및 **종료 날짜** 매개 변수`st` ( `se` 및 부분 문자열)는와 `11-02-2017T00:00:00Z`같은 전체 날짜/시간 형식 이어야 합니다. 축약 된 버전이 잘못 되었습니다. Azure CLI 일부 명령은 기본적으로 축약 된 값을 생성할 수 있습니다. |
|  |  |  |

자세한 내용은 [SAS (공유 액세스 서명) 사용](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)을 참조 하세요.
