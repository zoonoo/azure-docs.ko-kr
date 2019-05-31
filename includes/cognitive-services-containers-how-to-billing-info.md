---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: 05961f8dd2788179a15e6bfe094484cf4770067b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124310"
---
컨테이너에 대한 쿼리는 `<ApiKey>`에 사용되는 Azure 리소스의 가격 책정 계층으로 청구됩니다.

Azure Cognitive Services 컨테이너는 측정을 위해 청구 엔드포인트에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 사용자는 컨테이너가 항상 청구 엔드포인트와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다. 

### <a name="connect-to-azure"></a>Azure에 연결

컨테이너에는 실행할 청구 인수 값이 필요합니다. 이러한 값을 통해 컨테이너는 청구 엔드포인트에 연결할 수 있습니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다. 컨테이너가 허용되는 시간 내에서 Azure에 연결되지 않으면 컨테이너는 계속 실행되지만 청구 엔드포인트가 복원될 때까지 쿼리를 처리하지 않습니다. 10 ~ 15분 간격으로 동시에 10회 동안 연결이 시도됩니다. 10회 안에 청구 엔드포인트에 연결할 수 없는 경우 컨테이너는 실행을 중지합니다. 

### <a name="billing-arguments"></a>청구 인수

`docker run` 명령이 컨테이너를 시작하도록 다음 옵션 세 가지는 모두 유효한 값으로 지정해야 합니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 Cognitive Services 리소스의 API 키입니다.<br/>이 옵션의 값은 `Billing`에 지정된 프로비저닝된 리소스에 대한 API 키로 설정해야 합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 Cognitive Services 리소스의 엔드포인트입니다.<br/>이 옵션의 값은 프로비저닝된 Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 `accept`로 설정해야 합니다. |


