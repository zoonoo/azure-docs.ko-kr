---
title: Speech 서비스 지역 | Microsoft Docs
description: Speech 서비스의 지역에 대한 참조입니다.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/27/2018
ms.author: mahilleb
ms.openlocfilehash: a201cc043f673e2285ea48950804d97b96f881ed
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054944"
---
# <a name="regions-and-endpoints-of-the-speech-service"></a>Speech 서비스 지역 및 엔드포인트

> [!NOTE]
> [Speech SDK](speech-sdk.md)의 지역 이름은 아래 엔드포인트 도메인의 첫 부분과 일치합니다.
> 예를 들어, Speech SDK에서 미국 지역을 지정하려면 `westus`를 사용합니다.

## <a name="speech-to-text"></a>음성 텍스트 변환

[!include[](includes/endpoints-speech-to-text.md)]

## <a name="text-to-speech"></a>텍스트에서 음성 변환

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="authentication"></a>인증

[!include[](includes/endpoints-token-service.md)]

인증 토큰 가져오기 및 새로 고침에 대한 자세한 내용은 [여기](rest-apis.md#authentication)를 참조하세요.

## <a name="language-understanding-speech-sdk-only"></a>언어 이해(Speech SDK만 해당)

Language Understanding 서비스의 지역 목록은 [여기](/azure/cognitive-services/luis/luis-reference-regions)에 있습니다.
Speech SDK에서 엔드포인트 도메인 이름의 처음 부분으로 이 지역을 지정합니다(예: `westus`).
