---
title: Mobile Engagement 개념 | Microsoft Docs
description: Azure Mobile Engagement 개념
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8d19abd1-0a6c-4772-9fa5-5e99980ac5da
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1bc09de37c1b2aca35ef1ea74227df770f15baf5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-concepts"></a>Azure Mobile Engagement 개념
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

Mobile Engagement는 지원되는 모든 플랫폼에 공통되는 몇 가지 개념을 정의합니다. 이 문서에서는 이러한 개념을 간략하게 설명합니다.

Mobile Engagement를 처음 사용하는 경우 이 문서부터 참조하면 좋습니다. 또한 사용 중인 플랫폼과 관련된 문서를 읽어보세요. 자세한 정보 및 예제뿐만 아니라 가능한 제한 사항을 통해 이 문서에 설명된 개념을 구체적으로 설명해 줍니다.

## <a name="devices-and-users"></a>장치 및 사용자
Mobile Engagement는 각 장치에 대한 고유 식별자를 생성하여 사용자를 식별합니다. 이 식별자를 장치 식별자(또는 `deviceid`)라고 합니다. 식별자는 동일한 장치에서 실행하는 모든 응용 프로그램이 동일한 장치 식별자를 공유하는 방식으로 생성됩니다.

암시적으로 Mobile Engagement는 한 장치가 정확히 한 명의 사용자에게 속하는 것으로 간주하므로, 사용자와 장치는 동등한 개념입니다.

## <a name="sessions-and-activities"></a>세션 및 작업
세션은 사용자가 응용 프로그램을 한 번 사용하는 것으로, 사용을 시작한 시간부터 중지할 때까지의 시간입니다.

작업은 사용자가 수행하는 응용 프로그램의 지정된 하위 부분에 대한 한 번의 사용입니다(일반적으로 한 화면이지만 응용 프로그램에 적합한 어떤 항목이든 될 수 있음).

사용자는 한번에 하나의 동작만 수행할 수 있습니다.

작업은 이름(64자로 제한됨)으로 식별되고 선택적으로 추가 데이터(1024바이트로 제한됨)를 일부 포함할 수 있습니다.

세션은 사용자가 수행한 작업의 시퀀스에서 자동으로 계산됩니다. 사용자가 첫 번째 작업을 시작할 때 세션이 시작되고 마지막 작업을 끝내면 세션이 중지됩니다. 즉, 세션을 명시적으로 시작하거나 중지할 필요가 없습니다. 대신, 작업이 명시적으로 시작되거나 중지됩니다. 작업이 보고되지 않으면 세션이 보고되지 않습니다.

## <a name="events"></a>이벤트
이벤트는 인스턴트 동작(예: 사용자가 단추 누름 또는 문서 읽음)을 보고하는 데 사용됩니다.

이벤트는 현재 세션 또는 실행 중인 작업과 관련될 수 있거나 독립 실행형이 될 수 있습니다.

이벤트는 이름(64자로 제한됨)으로 식별되고 선택적으로 추가 데이터(1024바이트로 제한됨)를 일부 포함할 수 있습니다.

## <a name="error"></a>오류
오류는 응용 프로그램에서 올바르게 감지하는 문제(예: 잘못된 사용자 작업 또는 API 호출 실패)를 보고하는 데 사용됩니다.

오류는 현재 세션 또는 실행 중인 작업과 관련될 수 있거나 독립 실행형이 될 수 있습니다.

오류는 이름(64자로 제한됨)으로 식별되고 선택적으로 추가 데이터(1024바이트로 제한됨)를 일부 포함할 수 있습니다.

## <a name="job"></a>작업
작업은 기간(예: API 호출 기간, 광고의 표시 시간, 백그라운드 작업의 기간 또는 사용자 작업의 기간)이 있는 동작을 보고하는 데 사용됩니다.

사용자 개입 없이 작업을 백그라운드에서 수행할 수 있으므로 작업은 특정 세션과 관련되지 않습니다.

작업은 이름(64자로 제한됨)으로 식별되고 선택적으로 추가 데이터(1024바이트로 제한됨)를 일부 포함할 수 있습니다.

## <a name="crash"></a>크래시
크래시는 Mobile Engagement SDK에서 응용 프로그램에 의해 검색되지 않는 문제가 크래시로 지정되는 응용 프로그램 오류를 보고하기 위해 자동으로 발급합니다.

## <a name="application-information"></a>응용 프로그램 정보
응용 프로그램 정보(또는 앱 정보)는 일부 데이터를 응용 프로그램의 사용자와 연결하기 위해 사용자를 태그하는 데 사용됩니다. 즉, 앱 정보가 Azure Mobile Engagement 플랫폼의 서버 측에 저장되는 점을 제외하면 웹 쿠키와 비슷합니다.

Mobile Engagement SDK의 API 또는 Mobile Engagement 플랫폼의 장치 API를 사용하여 앱 정보를 등록할 수 있습니다.

앱 정보는 장치에 연결된 키/값 쌍입니다. 키는 앱 정보의 이름(64자의 ASCII 문자 [zA-A-z], [0-9] 숫자 및 밑줄 [_]로 제한됨)입니다. 값(1024자로 제한됨)은 문자열, 정수, 날짜(yyyy-MM-dd) 또는 부울(true 또는 false)이 될 수 있습니다.

앱 정보는 개수에 관계없이 Mobile Engagement 가격 조건으로 정의된 제한 내에서 장치에 연결할 수 있습니다. 지정된 단일 키에 대해 Mobile Engagement는 최신 값 집합의 추적(기록 제외)만 유지합니다. 앱 정보의 값을 설정 또는 변경하면 Mobile Engagement에서 이 앱 정보(있는 경우)에 대한 대상 기준을 다시 평가합니다. 즉, 앱 정보를 사용하여 실시간 푸시를 트리거할 수 있습니다.

## <a name="extra-data"></a>추가 데이터
추가 데이터(또는 기타 데이터)는 이벤트, 오류, 작업 및 작업에 연결할 수 있는 임의의 데이터입니다.

추가 데이터는 JSON 개체와 비슷하게 구조화됩니다. 이러한 데이터는 키/값 쌍의 트리로 구성됩니다. 키가 64자의 ASCII 문자 [zA-A-z], 숫자 [0-9] 및 밑줄 [_]로 제한되며 추가 데이터의 총 크기는 1024 자(Mobile Engagement SDK에 의해 JSON에서 한번 인코딩됨)로 제한됩니다.

키/값 쌍의 전체 트리는 JSON 개체로 저장됩니다. 그러나 첫 번째 수준의 키/값은 Segments와 같은 일부 고급 함수에서 직접 액세스할 수 있도록 구성 해제됩니다. 예를 들어 지난 달에 추가 키 "content_type"이 "scifi" 값으로 설정된 "content_viewed"라는 이벤트를 10번 이상 전송한 모든 사용자로 구성된 "SciFi 팬" 세그먼트를 쉽게 정의할 수 있습니다. 따라서 스칼라 값(예: 문자열, 날짜, 정수 또는 부울)을 사용하여 키/값 쌍의 간단한 목록으로 구성된 기타 데이터만 보내는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Mobile Engagement의 Windows 유니버설 SDK 개요](mobile-engagement-windows-store-sdk-overview.md)
* [Azure Mobile Engagement의 Windows Phone Silverlight SDK 개요](mobile-engagement-windows-phone-sdk-overview.md)
* [Azure Mobile Engagement용 iOS SDK](mobile-engagement-ios-sdk-overview.md)
* [Azure Mobile Engagement용 Android SDK](mobile-engagement-android-sdk-overview.md)

