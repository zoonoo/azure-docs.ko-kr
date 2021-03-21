---
title: Azure Notification Hubs 템플릿
description: Azure Notification Hubs에 대 한 템플릿 사용에 대해 알아봅니다.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635645"
---
# <a name="notification-hubs-templates"></a>Notification Hubs 템플릿

템플릿을 사용하여 클라이언트 애플리케이션이 받고자 하는 알림의 정확한 형식을 지정할 수 있습니다. 템플릿을 사용하면 앱이 다음을 포함한 여러 가지 이점을 얻을 수 있습니다.

- 플랫폼 제약이 없는 백 엔드
- 개인화된 알림
- 클라이언트 버전 독립성
- 간편한 지역화

이 섹션에서는 템플릿을 사용 하 여 플랫폼 간 모든 장치를 대상으로 하는 플랫폼 독립적인 알림을 보내고 각 장치에 대 한 브로드캐스트 알림을 개인 설정 하는 방법에 대 한 두 가지 자세한 예제를 제공 합니다.

## <a name="using-templates-cross-platform"></a>플랫폼 간 템플릿 사용

푸시 알림을 보내는 표준 방법은 보낼 각 알림에 대해 특정 페이로드를 플랫폼 알림 서비스(WNS, APNS)에 보내는 것입니다. 예를 들어 APNS에 경고를 보내려면 페이로드는 다음 형식의 JSON 개체입니다.

```json
{"aps": {"alert" : "Hello!" }}
```

Windows 스토어 애플리케이션에서 유사한 토스트 메시지를 보내기 위한 XML 페이로드는 다음과 같습니다.

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

MPNS(Windows Phone) 및 FCM(Android) 플랫폼에 대해 비슷한 페이로드를 만들 수 있습니다.

이 요구 사항 때문에 앱 백 엔드가 각 플랫폼에 대해 서로 다른 페이로드를 생성하며 결국 백 엔드는 앱의 프레젠테이션 계층의 일부를 담당합니다. 이 경우 지역화 및 그래픽 레이아웃(특히 여러 유형의 타일에 대한 알림을 포함하는 Windows 스토어 앱의 경우)을 포함한 몇 가지 문제가 있습니다.

Notification Hubs 템플릿 기능을 사용하여 클라이언트 앱은 태그 집합 외에 템플릿도 포함하는 템플릿 등록이라는 특수 등록을 만들 수 있습니다. Notification Hubs 템플릿 기능을 사용 하면 설치 (기본 설정) 또는 등록을 사용 하는지 여부에 관계 없이 클라이언트 앱에서 장치를 템플릿에 연결할 수 있습니다. 앞의 페이로드 예제에서 플랫폼 독립적인 유일한 정보는 실제 경고 메시지 (**Hello!**)입니다. 템플릿은 특정 클라이언트 앱을 등록 하기 위해 플랫폼 독립적인 메시지의 형식을 지정 하는 방법에 대 한 알림 허브의 지침 집합입니다. 앞의 예제에서 플랫폼 독립적 메시지는 `message = Hello!` 단일 속성입니다.

다음 그림에 이러한 프로세스가 나와 있습니다.

![플랫폼 간 템플릿 사용 프로세스를 보여 주는 다이어그램](./media/notification-hubs-templates/notification-hubs-hello.png)

iOS 클라이언트 앱 등록에 대한 템플릿은 다음과 같습니다.

```json
{"aps": {"alert": "$(message)"}}
```

Windows 스토어 클라이언트 앱에 대한 해당 템플릿은 다음과 같습니다.

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

식에 대 한 실제 메시지를 대체 합니다 `$(message)` . 이 식은이 특정 등록에 메시지를 보낼 때마다 알림 허브에 지시 하 여 그 뒤에 오는 메시지를 빌드하고 공통 값을 삽입 합니다.

설치 모델을 사용 하는 경우 설치 "템플릿" 키에 여러 템플릿의 JSON이 포함 됩니다. 등록 모델로 작업 하는 경우 여러 템플릿을 사용 하기 위해 클라이언트 응용 프로그램에서 여러 등록을 만들 수 있습니다. 예를 들어 경고 메시지에 대 한 템플릿과 타일 업데이트에 대 한 템플릿이 있습니다. 또한 클라이언트 애플리케이션은 기본 등록(템플릿이 없는 등록)과 템플릿 등록을 혼합할 수 있습니다.

알림 허브는 동일한 클라이언트 앱에 속하는지 여부를 고려 하지 않고 각 템플릿에 대해 알림을 하나 보냅니다. 이 동작을 사용하면 플랫폼 독립적인 알림을 더 많은 알림으로 변환할 수 있습니다. 예를 들어 알림 허브에 대 한 동일한 플랫폼 독립적 메시지를 백 엔드에서 인식 하지 않아도 알림 허브에 대 한 동일한 플랫폼 독립적 메시지를 알림 및 타일 업데이트에서 원활 하 게 변환할 수 있습니다. 일부 플랫폼 (예: iOS)은 짧은 기간 내에 전송 되는 경우 여러 알림을 동일한 장치로 축소할 수 있습니다.

## <a name="using-templates-for-personalization"></a>개인 설정에 템플릿 사용

템플릿 사용의 또 다른 장점은 Notification Hubs를 사용하여 알림의 등록별 개인 설정을 수행할 수 있다는 것입니다. 예를 들어 날씨 조건이 있는 타일을 특정 위치에 표시 하는 날씨 앱을 생각해 보겠습니다. 사용자는 섭씨 또는 화씨 온도 및 1일 또는 5일 예보 중에서 선택할 수 있습니다. 템플릿을 사용하여 각 클라이언트 앱 설치에서 필요한 형식(1일 섭씨, 1일 화씨, 5일 섭씨, 5일 화씨)에 대해 등록하고 템플릿을 채우는 데 필요한 모든 정보가 포함된 단일 메시지(예를 들어 섭씨 및 화씨 도가 포함된 5일 예보)를 백 엔드가 보내도록 합니다.

섭씨 온도가 포함된 1일 예보용 템플릿은 다음과 같습니다.

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

알림 허브로 전송 되는 메시지에는 다음 속성이 모두 포함 됩니다.

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

이 패턴을 사용하면 백 엔드는 앱 사용자에 대한 특정 개인 설정 옵션을 저장할 필요 없이 단일 메시지만 보냅니다. 다음 그림은 이 시나리오를 보여줍니다.

![백 엔드가 각 플랫폼에 단일 메시지만 보내는 방법을 보여 주는 다이어그램입니다.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>템플릿을 등록하는 방법

설치 모델 (기본 설정) 또는 등록 모델을 사용 하 여 템플릿에 등록 하려면 [등록 관리](notification-hubs-push-notification-registration-management.md)를 참조 하세요.

## <a name="template-expression-language"></a>템플릿 식 언어

템플릿은 XML 또는 JSON 문서 형식으로 제한됩니다. 또한 특정 위치에 식을 배치할 수 있습니다. 예를 들어 XML에 대 한 노드 특성 또는 값, JSON에 대 한 문자열 속성 값이 있습니다.

다음 표는 템플릿에 허용되는 언어를 보여 줍니다.

| 식       | 설명 |
| ---------------- | --- |
| $(prop)          | 지정한 이름을 가진 이벤트 속성에 대한 참조입니다. 속성 이름은 대/소문자를 구분하지 않습니다. 이 식은 속성이 없으면 속성의 텍스트 값 또는 빈 문자열로 확인됩니다. |
|$(prop, n)       | 위와 같이 텍스트는 n 자에서 명시적으로 잘립니다. 예를 들어 $(title, 20)은 title 속성의 내용을 20 자에서 자릅니다. |
| .(prop, n)      | 위와 같지만 잘릴 때 뒤에 점 세 개가 붙습니다. 잘린 문자열의 총 크기와 접미사가 n 자를 초과 하지 않습니다. (title, 20) 입력 속성이 "제목 줄입니다." 라고 하는 결과는 다음과 **같습니다.** |
| %(prop)          | 출력이 URI 인코딩된다는 것을 제외하고 $(name)과 유사합니다. |
| #(prop)          | JSON 템플릿(예: iOS 및 Android 템플릿)에 사용됩니다.<br><br>이 함수는 JSON 템플릿 (예: Apple 템플릿)에서 사용 되는 경우를 제외 하 고 이전에 지정 된 ' $ (prop) '과 똑같이 작동 합니다. 이 경우이 함수는 "{', '}" (예: ' myJsonProperty ': ' # (name) ')로 둘러싸여 있지 않으며 JavaScript 형식의 숫자로 계산 됩니다 (예: regexp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)? 출력 JSON은 숫자입니다.<br><br>예를 들어 ' 배지: ' # (name) '은 ' 배지 ': 40 (' 40 '이 아님)이 됩니다. |
| ' text ' 또는 "text" | 리터럴입니다. 리터럴에는 작은따옴표 또는 큰따옴표로 묶인 임의의 텍스트가 포함됩니다. |
| expr1 + expr2    | 두 개의 식을 단일 문자열로 합치는 연결 연산자입니다. |

위의 모든 형태가 식이 될 수 있습니다.

연결을 사용할 경우 전체 식을 `{}`로 묶어야 합니다. 예: `{$(prop) + ‘ - ’ + $(prop2)}`

예를 들어 다음 템플릿은 올바른 XML 템플릿이 아닙니다.

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

앞에서 설명한 대로 연결을 사용하는 경우 식을 중괄호로 묶어야 합니다. 예를 들면 다음과 같습니다.

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>다음 단계

[Azure Notification Hubs에 대 한 자세한 정보](notification-hubs-push-notification-overview.md)
