---
title: SMS FAQ
titleSuffix: An Azure Communication Services concept document
description: SMS FAQ
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646315"
---
# <a name="sms-faq"></a>SMS FAQ

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>고객이 응급 목적으로 Azure 통신 서비스를 사용할 수 있나요?

Azure 통신 서비스는 미국에서 텍스트-911 기능을 지원 하지 않지만 FCC (연방 통신 위원회)의 규칙에 따라이 작업을 수행할 의무가 있을 수 있습니다.  FCC의 텍스트-911 규칙이 서비스 또는 응용 프로그램에 적용 되는지 여부를 평가 해야 합니다. 이러한 규칙이 적용 되는 범위에 대해 911 문자 메시지를 요청 하는 비상 콜 센터로 라우팅하는 책임이 있습니다. 자신의 텍스트-911 배달 모델을 자유롭게 확인할 수 있지만, FCC에서 허용 하는 한 가지 방법에는 기본 모바일 통신 사업자를 통해 911 텍스트를 제공 하기 위해 사용자의 모바일 장치에 대 한 기본 전화 걸기가 자동으로 시작 됩니다.

## <a name="are-there-any-limits-on-sending-messages"></a>메시지 전송에 제한이 있나요?

Sla와 일치 하는 높은 서비스 품질을 계속 제공 하기 위해 Azure 통신 서비스는 각 기본에 대해 서로 다른 요금 제한을 적용 합니다. 한도를 초과 하는 Api를 호출 하는 개발자는 429 HTTP 상태 코드 응답을 받게 됩니다. 회사에서 요금 제한을 초과 하는 요구 사항이 있는 경우에 전자 메일을 보내 주세요 phone@microsoft.com .

SMS의 요금 제한:

|작업|범위|기간 (초)| 제한 (요청 #) | 분당 메시지 단위|
|---------|-----|-------------|-------------------|-------------------------|
|메시지 보내기|숫자 당|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Azure 통신 서비스는 무료 전화 번호에 대 한 옵트아웃을 어떻게 처리 하나요?

미국 수신자 부담 번호에 대한 옵트아웃은 미국 이동 통신 사업자에게 의무화되어 시행됩니다.
- STOP - 문자 메시지 수신자가 옵트아웃하려면 수신자 부담 번호로 'STOP'을 보내면 됩니다. 반송파는 중지에 대해 다음과 같은 기본 응답을 보냅니다. "네트워크 메시지:이 숫자에서 보낸 모든 텍스트를 차단 하는" stop "이라는 단어를 사용 하 여 회신 했습니다. 메시지를 다시 받으려면 "중지"를 다시 텍스트 합니다. "
- START/UNSTOP - 수신자가 수신자 부담 번호로부터 온 문자 메시지를 다시 수신하려면 수신자 부담 번호로 ‘START’ 또는 ‘UNSTOP’을 보내면 됩니다. 이동 통신 사업자는 START/UNSTOP에 대해 다음과 같은 기본 응답을 보냅니다. “네트워크 메시지: “unstop”을 회신했으며 이 번호로부터 메시지를 다시 수신하기 시작합니다.”
- Azure Communication Services는 STOP 메시지를 탐지하여 수신자에게 보내는 모든 메시지를 차단합니다. 전달 보고서에는 "지정된 받는 사람에 대해 보낸 사람이 차단됨"이라는 상태 메시지가 포함된 전달 실패가 표시됩니다.
- STOP, UNSTOP 및 START 메시지는 사용자에게 다시 릴레이됩니다. Azure Communication Services는 이러한 옵트아웃을 모니터링하고 구현하여 통신을 옵트아웃한 수신자에게 더 이상 메시지가 전송되지 않도록 합니다.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Azure Communication Services를 사용 하 여 메시지를 받으려면 어떻게 해야 하나요?

Azure Communication Services 고객은 Azure Event Grid를 사용 하 여 들어오는 메시지를 받을 수 있습니다. 이 [빠른](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) 시작에 따라 메시지를 받도록 이벤트 표를 설정 합니다.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>긴 메시지 (>2048 문자)를 보내거나 받을 수 있나요?

Azure 통신 서비스는 SMS를 통해 긴 메시지의 송수신을 지원 합니다. 그러나 일부 무선 매개체 또는 장치는 긴 메시지를 받을 때 다르게 작동할 수 있습니다.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>유선전화/숫자로 전송 된 메시지는 어떻게 처리 되나요?

미국에서 Azure Communication Services는 유선전화/번호를 확인 하지 않으며 배달를 위해 회사에 전송 하려고 시도 합니다. 유선전화/숫자로 전송 되는 메시지에 대 한 요금이 고객에 게 청구 됩니다. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>여러 받는 사람에 게 메시지를 보낼 수 있나요?


예, 여러 수신자를 사용 하 여 하나의 요청을 만들 수 있습니다. 이 [빠른](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) 시작을 따라 여러 받는 사람에 게 메시지를 보냅니다.
