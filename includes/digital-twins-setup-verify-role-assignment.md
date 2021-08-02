---
author: baanders
description: Azure Digital Twins 설정에서 역할 할당을 확인하기 위한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 044342122c2bd1d2b59a7fc3abb4ed6ca7bbc05f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110473848"
---
역할 할당을 성공적으로 설정했는지 확인하는 한 가지 방법은 [Azure Portal](https://portal.azure.com)에서 Azure Digital twins 인스턴스에 대한 역할 할당을 확인하는 것입니다. Azure Portal에서 Azure Digital Twins 인스턴스로 이동합니다([Azure Digital Twins 인스턴스](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 페이지에서 조회하거나 포털 검색 창에서 해당 이름을 검색할 수 있음).

그런 다음, *액세스 제어(IAM) > 역할 할당* 에서 할당된 모든 역할을 확인합니다. 사용자는 *Azure Digital Twins 데이터 소유자* 역할로 목록에 표시되어야 합니다. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스에 대한 역할 할당의 스크린샷":::