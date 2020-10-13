---
title: RabbitMQ와 Azure Service Bus를 통합 하는 방법
description: Azure Service Bus와 RabbitMQ를 통합 하는 방법에 대 한 단계별 가이드
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91301108"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>RabbitMQ와 Azure Service Bus를 통합 하는 방법

이 가이드에서는 RabbitMQ에서 Azure Service Bus로 메시지를 보내는 방법에 대해 알아봅니다.

다음은 이러한 기능을 사용할 수 있는 몇 가지 시나리오입니다.

- **Edge**설정: RabbitMQ에 메시지를 전송 하는 edge 설정이 있지만 추가 처리를 위해 이러한 메시지를 [Azure Service Bus](./service-bus-messaging-overview.md) 전달 하려고 하므로 많은 [Azure 빅 데이터 기능](/azure/architecture/guide/architecture-styles/big-data)을 사용할 수 있습니다.
- **하이브리드 클라우드**: 회사는 자신의 메시징 요구에 RabbitMQ를 사용 하는 제 3 자를 획득 한 것입니다. 서로 다른 클라우드에 있습니다. Azure로 전환 하는 동안에는 RabbitMQ를 사용 하 여 Azure Service Bus를 브리징 하 여 데이터 공유를 시작할 수 있습니다.
- 타사 **통합**: 제 3 자는 RabbitMQ을 broker로 사용 하 고 데이터를 microsoft에 전송 하려고 하지만 조직 외부에 있습니다. 메시지를 전달할 수 있는 제한 된 Azure Service Bus 큐 집합에 대 한 액세스 권한을 제공 하는 SAS 키를 제공할 수 있습니다.

목록이 제공 되지만, Azure에 RabbitMQ를 브리징 하 여 대부분의 이러한 사용 사례를 해결할 수 있습니다.

먼저 [여기](https://azure.microsoft.com/free/) 에서 등록 하 여 무료 Azure 계정을 만들어야 합니다.

계정에 로그인 하 고 나면 [Azure Portal](https://portal.azure.com/) 로 이동 하 여 새 Azure Service Bus [네임 스페이스](./service-bus-create-namespace-portal.md)를 만듭니다. 네임 스페이스는 큐 및 항목과 같이 메시징 구성 요소가 라이브 되는 범위 지정 컨테이너입니다.

## <a name="adding-a-new-azure-service-bus-namespace"></a>새 Azure Service Bus 네임 스페이스 추가

Azure Portal에서 큼 더하기 단추를 클릭 하 여 새 리소스를 추가 합니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="리소스 만들기":::

그런 다음 통합을 선택 하 고 Azure Service Bus를 클릭 하 여 메시징 네임 스페이스를 만듭니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="리소스 만들기":::

네임 스페이스 정보를 입력 하 라는 메시지가 표시 됩니다. 사용할 Azure 구독을 선택합니다. 리소스 그룹이 없는 경우 새 [리소스 그룹](../azure-resource-manager/management/manage-resource-groups-portal.md)을 만들 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="리소스 만들기":::

`rabbitmq`에는 `Namespace name` 를 사용 하지만 원하는 모든 것이 될 수 있습니다. 그런 다음 `East US` 해당 위치에 대해를 설정 합니다. `Basic`가격 책정 계층으로 선택 합니다.

모든 것이 제대로 수행 되 면 다음과 같은 확인 화면이 표시 됩니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="리소스 만들기":::

그런 다음 Azure Portal 돌아가서 새 `rabbitmq` 네임 스페이스 목록이 표시 됩니다. 큐를 추가할 수 있도록 리소스를 클릭 하 여 리소스에 액세스 합니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="리소스 만들기":::

## <a name="creating-our-azure-service-bus-queue"></a>Azure Service Bus 큐 만들기

이제 Azure Service Bus 네임 스페이스가 있으므로의 왼쪽에 있는 단추를 클릭 하 여 `Queues` `Entities` 새 큐를 추가할 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="리소스 만들기":::

큐의 이름은 메시지의 출처를 알려 주는 것 `from-rabbitmq` 입니다. 다른 모든 옵션을 기본값으로 그대로 둘 수 있지만 응용 프로그램의 필요에 맞게 변경할 수 있습니다.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>RabbitMQ Shovel 플러그 인 사용

RabbitMQ에서 Azure Service Bus로 메시지를 배송 하려면 RabbitMQ와 함께 제공 되는 [Shovel 플러그 인](https://www.rabbitmq.com/shovel.html) 을 사용 하겠습니다. 다음 명령을 사용 하 여 플러그 인 및 해당 시각적 인터페이스를 사용 하도록 설정할 수 있습니다.

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Root로 해당 명령을 실행 해야 할 수도 있습니다.

이제 RabbitMQ를 Azure에 연결 하는 데 필요한 자격 증명을 가져올 시간입니다.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Azure Service Bus에 RabbitMQ 연결

RabbitMQ에서 메시지를 게시할 수 있도록 큐에 대 한 SAS ( [공유 액세스 정책](../storage/common/storage-sas-overview.md) )를 만들어야 합니다. SAS 정책을 사용 하면 리소스에서 수행할 수 있는 외부 파티가 무엇 인지 지정할 수 있습니다. RabbitMQ는 메시지를 보낼 수 있지만 큐를 수신 하거나 관리 하지는 않습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="리소스 만들기":::

상자를 `Send` 클릭 한 다음 `Create` SAS 정책을 적용 하려면 클릭 합니다.

정책을 만든 후에는이를 클릭 하 여 **기본 연결 문자열**을 확인 합니다. 이를 사용 하 여 RabbitMQ에 Azure Service Bus 대 한 의견을 보내 주세요.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="리소스 만들기":::

이 연결 문자열을 사용 하려면 먼저 RabbitMQ의 AMQP 연결 형식으로 변환 해야 합니다. [연결 문자열 변환기 도구로](https://red-mushroom-0f7446a0f.azurestaticapps.net/) 이동 하 여 연결 문자열을 양식에 붙여 넣은 후 변환을 클릭 합니다. RabbitMQ 준비 된 연결 문자열을 가져옵니다. (해당 웹 사이트는 데이터를 네트워크를 통해 전송 하지 않도록 브라우저에서 로컬을 모두 실행 합니다). [GitHub](https://github.com/videlalvaro/connstring_to_amqp)의 소스 코드에 액세스할 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="리소스 만들기":::

이제 브라우저에서 RabbitMQ 관리 플러그 인을 열고 `http://localhost:15672/#/dynamic-shovels` 로 이동 `Admin -> Shovel Management` 합니다. 여기서 RabbitMQ 큐의 메시지를 Azure Service Bus 큐로 보내는 새 shovel을 추가할 수 있습니다.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="리소스 만들기":::

여기에서 Shovel `azure` 을 호출 하 고를 `AMQP 0.9.1` 원본 프로토콜로 선택 합니다. 스크린샷에는 `amqp://` 로컬 RabbitMQ 서버에 연결 하는 기본 URI가 있습니다. 현재 배포에 맞게 조정 해야 합니다.

큐 쪽에서를 `azure` 큐의 이름으로 사용할 수 있습니다. 해당 큐가 없으면 RabbitMQ가 해당 큐를 만듭니다. 이미 존재 하는 큐의 이름을 선택할 수도 있습니다. 다른 옵션은 기본값으로 그대로 둘 수 있습니다.

그런 다음 `destination` 의 측면에서를 프로토콜로 선택 합니다 `AMQP 1.0` . `URI`이전 단계에서 얻은 연결 문자열을 필드에 입력 합니다. Azure 연결 문자열을 RabbitMQ 형식으로 변환 했습니다. 다음과 같이 표시됩니다.

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

필드에 `Address` **Azure Service Bus 큐**의 이름을 입력 합니다 .이 경우에는가 호출 되었습니다 `from-rabbitmq` . 을 클릭 하면 설치 프로그램에서 `Add Shovel` 메시지 수신을 시작할 준비가 된 것입니다.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>RabbitMQ에서 Azure Service Bus로 메시지 게시

RabbitMQ 관리 인터페이스에서 `Queues` 큐를 선택 하 `azure` 고 패널을 검색 합니다 `Publish message` . 메시지를 큐에 직접 게시할 수 있는 양식이 표시 됩니다. 이 예에서는를로 추가 하 `fist message` `Payload` 고 적중 합니다 `Publish Message` .

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="리소스 만들기":::

Azure로 돌아가서 큐를 검사 합니다. `Service Bus Explorer`왼쪽 패널을 클릭 한 다음 _피킹 (peeking_ ) 단추를 클릭 합니다. 모든 문제가 정상적으로 완료 되 면 이제 큐에 하나의 메시지가 표시 됩니다. Yay, 축 하 합니다!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="리소스 만들기":::

하지만 메시지가 RabbitMQ에서 보낸 메시지 인지 확인 합니다. 탭을 선택 `Peek` 하 고 단추를 클릭 `Peek` 하 여 큐에 있는 마지막 메시지를 검색 합니다. 메시지를 클릭 하 여 내용을 검사 합니다. 아래 이미지와 유사한 항목이 표시 됩니다 `first message` .

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="리소스 만들기":::

## <a name="lets-recap"></a>요약 하겠습니다.

축하합니다! 많은 것을 달성 했습니다. RabbitMQ에서 Azure Service Bus로 메시지를 가져오기 위해 관리 하는 단계를 살펴보겠습니다.

1. Azure Service Bus 네임 스페이스 만들기
2. 네임 스페이스에 큐 추가
3. 큐에 SAS 정책 추가
4. 큐 연결 문자열 가져오기
5. 관리 인터페이스 & RabbitMQ shovel 플러그 인을 사용 하도록 설정 합니다.
6. Azure Service Bus 연결 문자열을 RabbitMQ의 AMQP 형식으로 변환 합니다.
7. RabbitMQ에 새 Shovel을 추가 하 여 Azure Service Bus에 연결 &
8. 메시지 게시

이전 단계를 수행 하 여 Azure 외부의 조직 영역을 통합 했습니다. Shovel 플러그 인을 통해 RabbitMQ에서 Azure Service Bus로 메시지를 배송할 수 있습니다. 이제 신뢰할 수 있는 타사가 Azure 배포에 앱을 연결 하도록 허용할 수 있으므로이는 상당한 이점이 있습니다.

결과적으로 메시징은 연결을 설정 하는 것 이며,이 기술을 사용 하면 새 연결을 열었습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Service Bus](./service-bus-messaging-overview.md)에 대해 자세히 알아보세요.
- [에서 Amqp 1.0 지원](./service-bus-amqp-overview.md) 에 대해 자세히 알아보세요 Service Bus
