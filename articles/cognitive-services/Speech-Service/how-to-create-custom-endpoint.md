---
title: Azure에서 음성 서비스를 사용 하 여 사용자 지정 음성 끝점 만들기 | Microsoft Docs
description: Azure 음성 서비스를 사용 하 여 사용자 지정 음성-텍스트 끝점을 만드는 방법에 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863001"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>사용자 지정 음성-텍스트 엔드포인트 만들기

사용자 지정 음향 모델 또는 언어 모델을 만든 후에 사용자 지정 음성-텍스트 변환 엔드포인트에 배포할 수 있습니다.

## <a name="create-an-endpoint"></a>엔드포인트 만들기
새로운 사용자 지정 엔드포인트를 만들려면 페이지 맨 위에 있는 **Custom Speech** 메뉴에서 **엔드포인트**를 선택합니다. 그러면 현재 사용자 지정 엔드포인트 표를 볼 수 있는 **엔드포인트** 페이지로 이동됩니다. 아직 엔드포인트를 만들지 않은 경우 표가 비어 있습니다. 현재 로캘이 표 제목에 반영됩니다.

다른 언어에 대한 배포를 만들려면 **로캘 변경**을 클릭합니다. 자세한 내용은 지원되는 언어를 참조하세요.

새 엔드포인트를 만들려면 **새로 만들기**를 선택합니다. **엔드포인트 만들기** 창에서 사용자 지정 배포의 **이름** 및 **설명** 상자에 정보를 입력합니다.

**구독** 콤보 상자에서 사용할 구독을 선택합니다. S0(유료) 구독인 경우 동시 요청을 선택할 수 있습니다.

콘텐츠 로깅을 켜거나 끌 수도 있습니다. 즉, 엔드포인트 트래픽을 저장할 것인지 여부를 선택할 수 있습니다. 선택하지 않으면 트래픽이 저장되지 않습니다. 기록된 모든 콘텐츠를 보려면 엔드포인트-> 세부 정보 보기 아래에서 다운로드 링크를 찾으면 됩니다.

**음향 모델** 목록에서 원하는 음향 모델을 선택하고, **언어 모델** 목록에서 원하는 언어 모델을 선택합니다. 음향 모델 및 언어 모델 선택 항목에는 항상 기본 Microsoft 모델이 포함됩니다. 기본 모델을 선택하면 조합이 제한됩니다. 대화형 기본 모델을 검색 및 받아쓰기 기본 모델과 혼합할 수 없습니다.

> [!NOTE]
> 확인란을 선택하여 사용 약관 및 가격 책정 정보에 동의해야 합니다.
>

음향 및 언어 모델을 선택한 후 **만들기**를 선택합니다. 그러면 **배포** 페이지로 돌아갑니다. 이제 새 엔드포인트에 해당하는 항목이 표에 포함되어 있습니다. 이 엔드포인트의 상태는 생성되는 동안 현재 상태를 반영합니다. 사용자 지정 모델을 사용하여 새 엔드포인트를 인스턴스화하는 데 최대 30분이 걸릴 수 있습니다. 배포 상태가 *완료*이면 엔드포인트를 사용할 준비가 완료된 것입니다.

배포 준비가 완료되면 엔드포인트 이름이 링크로 변합니다. 이 링크를 선택하면 웹 소켓을 사용하는 HTTP 요청 또는 Microsoft Cognitive Services Speech 클라이언트 라이브러리에 사용할 사용자 지정 엔드포인트 URL을 표시하는 **엔드포인트 정보** 페이지가 나타납니다.

## <a name="next-steps"></a>다음 단계

더 많은 자습서를 보려면 다음 항목을 참조하세요.
- [Speech Services 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [사용자 지정 어쿠스틱 모델 만들기](how-to-customize-acoustic-models.md)
- [사용자 지정 언어 모델 만들기](how-to-customize-language-model.md)
