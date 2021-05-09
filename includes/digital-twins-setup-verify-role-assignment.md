---
author: baanders
description: Azure Digital Twins 설정에서 역할 할당을 확인하기 위한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92489044"
---
역할 할당을 성공적으로 설정했는지 확인하는 한 가지 방법은 [Azure Portal](https://portal.azure.com)에서 Azure Digital twins 인스턴스에 대한 역할 할당을 확인하는 것입니다. Azure Portal에서 Azure Digital Twins 인스턴스로 이동합니다([Azure Digital Twins 인스턴스](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 페이지에서 조회하거나 포털 검색 창에서 해당 이름을 검색할 수 있음).

그런 다음, *액세스 제어(IAM) > 역할 할당* 에서 할당된 모든 역할을 확인합니다. 사용자는 *Azure Digital Twins 데이터 소유자* 역할로 목록에 표시되어야 합니다. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스에 대한 역할 할당 보기":::