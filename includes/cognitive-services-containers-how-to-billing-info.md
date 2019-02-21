---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: ce7d8628c28a4a202a05aeea60e71655b4b7f1a2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984922"
---
Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(발화)를 Microsoft로 보내지 않습니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

`docker run`는 청구 목적으로 다음 인수를 사용합니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 Cognitive Service 리소스의 API 키입니다.<br/>이 옵션의 값은 `Billing`에 지정된 프로비저닝된 리소스에 대한 API 키로 설정해야 합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 Cognitive Service 리소스의 엔드포인트입니다.<br/>이 옵션의 값은 프로비전된 LUIS Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 `accept`로 설정해야 합니다. |

> [!IMPORTANT]
> 세 가지 옵션을 모두 유효한 값으로 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.
