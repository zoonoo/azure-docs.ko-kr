---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: af4c3829c8b12bfcaae0602dde8f459de7e50f3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67182844"
---
- **주문 세부 정보**. 주문이 만들어지면 사용자의 배송 주소, 이메일 주소 및 연락처 정보가 Azure Portal에 저장됩니다. 저장되는 정보는 다음과 같습니다.
  - 담당자 이름
  - 전화 번호
  - 전자 메일 주소
  - 주소
  - City
  - ZIP 코드/우편 번호
  - 시스템 상태
  - 국가/시/도/지역
  - 배송 추적 번호

    주문 정보는 암호화되어 서비스에 저장됩니다. 서비스는 리소스 또는 주문을 명시적으로 삭제할 때까지 정보를 보존합니다. 리소스 삭제 및 해당 주문은 디바이스가 배송된 시점부터 디바이스가 Microsoft로 반환될 때까지 차단됩니다.

- **배송 주소**. 주문이 완료되면 Data Box 서비스는 UPS와 같은 타사 운송업체에 배송 주소를 제공합니다.

- **공유 사용자**. 디바이스의 사용자는 공유에 있는 데이터에도 액세스할 수 있습니다. 공유 데이터에 액세스할 수 있는 사용자 목록을 볼 수 있습니다. 공유가 삭제되면 이 목록도 삭제됩니다.