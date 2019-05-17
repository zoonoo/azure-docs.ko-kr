---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684653"
---
컨테이너에 대한 쿼리는 `<ApiKey>`로 사용된 Azure 리소스의 가격 책정 계층에서 청구됩니다.

Cognitive Services 컨테이너는 계량에 대한 청구 끝점에 연결하지 않고 실행하는 것은 허가되지 않습니다. 고객은 항상 청구 끝점을 사용하여 청구 정보를 통신하도록 컨테이너를 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다. 

### <a name="connecting-to-azure"></a>Azure에 연결

컨테이너는 실행하기 위한 청구 인수 값이 필요합니다. 이러한 값으로 컨테이너가 청구 끝점에 연결할 수 있습니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다. 컨테이너가 허용되는 시간 창 내에 Azure에 연결되지 않는 경우, 컨테이너는 계속 실행되지만 청구 끝점이 복원될 때까지 쿼리를 제공하지 않습니다. 연결은 10 ~ 15분의 동일한 시간 간격으로 10번 시도합니다. 10 회 내에 청구 끝점에 연결할 수 없는 경우 컨테이너 실행이 중지됩니다. 

### <a name="billing-arguments"></a>청구 인수

컨테이너를 시작하는`docker run` 명령을 위해 다음 세 옵션 모두를 유효한 값으로 지정해야 합니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 Cognitive Service 리소스의 API 키입니다.<br/>이 옵션의 값은 `Billing`에 지정된 프로비저닝된 리소스에 대한 API 키로 설정해야 합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 Cognitive Service 리소스의 끝점입니다.<br/>이 옵션의 값은 프로비전된 Azure 리소스의 URI 끝점으로 설정되어야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 `accept`로 설정해야 합니다. |


