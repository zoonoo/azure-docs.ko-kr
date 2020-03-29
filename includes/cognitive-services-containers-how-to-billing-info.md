---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77474128"
---
컨테이너에 대한 쿼리는 `ApiKey`에 사용되는 Azure 리소스의 가격 책정 계층에서 청구됩니다.

Azure Cognitive Services 컨테이너는 계량/청구 끝점에 연결하지 않고 실행하도록 라이선스가 부여되지 않습니다. 사용자는 컨테이너가 항상 청구 엔드포인트와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

### <a name="connect-to-azure"></a>Azure에 연결

컨테이너에는 실행할 청구 인수 값이 필요합니다. 이러한 값을 통해 컨테이너는 청구 엔드포인트에 연결할 수 있습니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다. 컨테이너가 허용되는 시간 내에서 Azure에 연결되지 않으면 컨테이너는 계속 실행되지만 청구 엔드포인트가 복원될 때까지 쿼리를 처리하지 않습니다. 10 ~ 15분 간격으로 동시에 10회 동안 연결이 시도됩니다. 10회 시도 내에서 청구 끝점에 연결할 수 없는 경우 컨테이너는 요청 제공을 중지합니다.

### <a name="billing-arguments"></a>청구 인수

다음 <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 세 가지 옵션 모두 유효한 값으로 제공될 때 명령이 컨테이너를 시작합니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 코그너티브 서비스 리소스의 API 키입니다.<br/>에 지정된 프로비저닝된 리소스에 대한 API 키로 설정해야 `Billing`합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 인지 서비스 리소스의 끝점입니다.<br/>이 옵션의 값은 프로비저닝된 Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값을 **허용하도록**설정해야 합니다. |
