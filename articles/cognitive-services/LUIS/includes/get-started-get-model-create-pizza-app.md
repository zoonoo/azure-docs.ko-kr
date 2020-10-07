---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 9965e4c856fdef2af17b116264ad5344ebc97eb2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "84466800"
---
Pizza 앱 만들기

1. [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json)을 선택하여 `pizza-app-for-luis.json` 파일에 대한 GitHub 페이지를 엽니다.
1. **Raw** 단추를 마우스 오른쪽 단추로 클릭하거나 길게 누르고 **다른 이름으로 링크 저장**을 선택하여 `pizza-app-for-luis.json`을 컴퓨터에 저장합니다.
1. [LUIS 포털](https://www.luis.ai)에 로그인합니다.
1. [내 앱](https://www.luis.ai/applications)을 선택합니다.
1. **내 앱** 페이지에서 **+ 대화용 새 앱**을 선택합니다.
1. **JSON으로 가져오기**를 선택합니다.
1. **새 앱 가져오기** 대화 상자에서 **파일 선택** 단추를 선택합니다.
1. 다운로드한 `pizza-app-for-luis.json` 파일을 선택한 다음, **열기**를 선택합니다.
1. **새 앱 가져오기** 대화 상자의 **이름** 필드에 Pizza 앱의 이름을 입력한 다음, **완료** 단추를 선택합니다.

앱을 가져옵니다.

**효과적인 LUIS 앱을 만드는 방법** 대화 상자가 표시되면 대화 상자를 닫습니다.

## <a name="train-and-publish-the-pizza-app"></a>Pizza 앱 학습 및 게시

**의도** 페이지가 Pizza 앱의 의도 목록과 함께 표시되어야 합니다.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Pizza 앱에 제작 리소스 추가

1. **관리**를 선택합니다.
1. **Azure 리소스**를 선택합니다.
1. **제작 리소스**를 선택합니다.
1. **제작 리소스 변경**을 선택합니다.

제작 리소스가 있는 경우 제작 리소스의 **테넌트 이름**, **구독 이름** 및 **LUIS 리소스 이름**을 입력합니다.

제작 리소스가 없는 경우

1. **새 리소스 만들기**를 선택합니다.
1. **테넌트 이름**, **리소스 이름**, **구독 이름** 및 **Azure 리소스 그룹 이름**을 입력합니다.

이제 Pizza 앱을 사용할 준비가 되었습니다.

## <a name="record-the-access-values-for-your-pizza-app"></a>피자 앱에 대한 액세스 값 기록

새 Pizza 앱을 사용하려면 Pizza 앱의 앱 ID, 제작 키 및 제작 엔드포인트가 필요합니다.

이러한 값을 찾으려면 다음을 수행합니다.

1. **의도** 페이지에서 **관리**를 선택합니다.
1. **애플리케이션 설정** 페이지에서 **앱 ID**를 기록합니다.
1. **Azure 리소스**를 선택합니다.
1. **제작 리소스**를 선택합니다.
1. **제작 리소스** 탭에서 **기본 키**를 기록합니다. 이 값은 제작 키입니다.
1. **엔드포인트 URL**을 기록합니다. 이 값은 제작 엔드포인트입니다.
