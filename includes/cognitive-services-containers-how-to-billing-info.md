---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "77474128"
---
컨테이너에 대 한 쿼리는에 사용 되는 Azure 리소스의 가격 책정 계층에서 청구 됩니다 `ApiKey`.

Azure Cognitive Services 컨테이너는 계량/청구 끝점에 연결 하지 않고 실행할 수 있는 권한이 없습니다. 사용자는 컨테이너가 항상 청구 엔드포인트와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

### <a name="connect-to-azure"></a>Azure 연결

컨테이너에는 실행할 청구 인수 값이 필요합니다. 이러한 값을 통해 컨테이너는 청구 엔드포인트에 연결할 수 있습니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다. 컨테이너가 허용되는 시간 내에서 Azure에 연결되지 않으면 컨테이너는 계속 실행되지만 청구 엔드포인트가 복원될 때까지 쿼리를 처리하지 않습니다. 10 ~ 15분 간격으로 동시에 10회 동안 연결이 시도됩니다. 10 번의 시도 내에서 청구 끝점에 연결할 수 없는 경우 컨테이너는 요청 처리를 중지 합니다.

### <a name="billing-arguments"></a>청구 인수

이 명령은 다음 중 세 가지 옵션을 모두 유효한 값으로 제공 하면 컨테이너를 시작 합니다. <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

| 옵션 | Description |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적 하는 데 사용 되는 Cognitive Services 리소스의 API 키입니다.<br/>이 옵션의 값은에 `Billing`지정 된 프로 비전 된 리소스에 대 한 API 키로 설정 되어야 합니다. |
| `Billing` | 청구 정보를 추적 하는 데 사용 되는 Cognitive Services 리소스의 끝점입니다.<br/>이 옵션의 값은 프로비저닝된 Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 **accept**로 설정 되어야 합니다. |
