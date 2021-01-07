---
author: baanders
description: Azure Digital Twins 설정에서 역할 할당을 확인 하는 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489044"
---
역할 할당을 성공적으로 설정 했는지 확인 하는 한 가지 방법은 [Azure Portal](https://portal.azure.com)에서 Azure Digital twins 인스턴스에 대 한 역할 할당을 확인 하는 것입니다. Azure Portal에서 Azure Digital Twins 인스턴스로 이동 합니다. ( [Azure Digital Twins 인스턴스의](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 페이지에서 확인 하거나 포털 검색 표시줄에서 해당 이름을 검색할 수 있습니다.)

그런 다음 *액세스 제어 (IAM) > 역할 할당*에서 할당 된 모든 역할을 확인 합니다. 사용자는 *Azure Digital Twins 데이터 소유자*역할을 사용 하 여 목록에 표시 되어야 합니다. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스에 대 한 역할 할당 보기":::