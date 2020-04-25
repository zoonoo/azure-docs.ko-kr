---
title: Azure Marketplace에 대 한 일반적인 SAS URL 문제 및 픽스
description: 공유 액세스 서명 URI 및 가능한 솔루션의 사용과 관련된 일반적인 문제를 나열합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147040"
---
# <a name="common-sas-url-issues-and-fixes"></a>일반적인 SAS URL 문제 및 수정

> [!IMPORTANT]
> 2020 년 4 월 13 일부 터 Azure Virtual Machine 제품의 관리를 파트너 센터로 전환 하기 시작 합니다. 마이그레이션 후 파트너 센터에서 제품을 만들고 관리 합니다. [일반적인 SAS URL 문제 및 픽스](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) 의 지침에 따라 마이그레이션된 제안을 관리 합니다.

다음 표에는 공유 액세스 서명(업로드된 솔루션용 VHD를 식별하고 공유하는 데 사용됨)을 사용할 때 발생하는 몇 가지 일반적인 문제가 제안된 해결 방법과 함께 나와 있습니다.

| **문제** | **오류 메시지** | **방법을** | 
| --------- | ------------------- | ------- | 
| &emsp;  *이미지 복사 중 오류* |  |  |
| "?"가 SAS URL에 없습니다 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 권장되는 도구를 사용하여 SAS URL을 업데이트합니다. |
| SAS URL에 없는 "st" 및 "se" 매개 변수 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URL을 적절한 **시작 날짜**와 **종료 날짜** 값으로 업데이트합니다. | 
| SAS URL에 "sp = rl"이 없습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | SAS URL을 `Read` 및 `List`로 설정된 권한으로 업데이트합니다. | 
| SAS URL의 VHD 이름에 공백이 있습니다. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 공백을 제거하도록 SAS URL을 업데이트합니다. |
| SAS URL 권한 부여 오류 | `Failure: Copying Images. Not able to download blob due to authorization error` | SAS URI 형식을 검토하고 수정합니다. 필요한 경우 다시 생성합니다. |
| "st" 및 "se" SAS URL 매개 변수에 전체 날짜/시간 사양이 없습니다. | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS URL **시작 날짜** 및 **종료 날짜** 매개 변수`st` ( `se` 및 부분 문자열)는와 `11-02-2017T00:00:00Z`같이 전체 날짜/시간 형식을 가져야 합니다. 약식 버전은 유효하지 않습니다. (Azure CLI의 일부 명령은 기본적으로 약식 값을 생성할 수 있습니다.) | 
|  |  |  |

자세한 내용은 [SAS(공유 액세스 서명) 사용](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)을 참조하세요.
