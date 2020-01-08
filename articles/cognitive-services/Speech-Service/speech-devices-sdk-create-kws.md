---
title: 사용자 지정 키워드 만들기-음성 서비스
titleSuffix: Azure Cognitive Services
description: 장치는 항상 키워드 (또는 구)를 수신 대기 합니다. 사용자가 키워드를 표시 하면 장치는 사용자가 말하기를 중단할 때까지 모든 후속 오디오를 클라우드로 보냅니다. 키워드를 사용자 지정 하는 것은 장치를 차별화 하 고 브랜딩을 강화 하는 효과적인 방법입니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 5bd7352230d4d9daaed219f654be51dc528bea8e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380187"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Speech Studio를 사용 하 여 사용자 지정 키워드 만들기

장치는 항상 키워드 (또는 구)를 수신 대기 합니다. 예를 들어 "안녕하세요 Cortana"는 Cortana 길잡이에 대 한 키워드입니다. 사용자가 키워드를 표시 하면 장치는 사용자가 말하기를 중단할 때까지 모든 후속 오디오를 클라우드로 보냅니다. 키워드를 사용자 지정 하는 것은 장치를 차별화 하 고 브랜딩을 강화 하는 효과적인 방법입니다.

이 문서에서는 장치에 대 한 사용자 지정 키워드를 만드는 방법에 대해 알아봅니다.

## <a name="create-your-keyword"></a>키워드 만들기

사용자 지정 키워드를 사용 하려면 [Speech Studio](https://aka.ms/sdsdk-speechportal)에서 [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지를 사용 하 여 키워드를 만들어야 합니다. 키워드를 제공 하면 장치에 배포 하는 파일이 생성 됩니다.

1. [Speech Studio](https://aka.ms/sdsdk-speechportal) 로 이동 하 여 **로그인** 하거나, 아직 음성 구독이 없는 경우 [**구독 만들기**](https://go.microsoft.com/fwlink/?linkid=2086754)를 선택 합니다.

1. [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지에서 **새 프로젝트**를 만듭니다. 

1. **이름**, **설명**(선택 사항)을 입력 하 고 언어를 선택 합니다. 언어 당 하나의 프로젝트가 필요 하 고 지원은 현재 en-us 언어로 제한 됩니다.

    ![키워드 프로젝트 설명](media/custom-keyword/custom-kws-portal-new-project.png)

1. 목록에서 프로젝트를 선택 합니다. 

    ![키워드 프로젝트를 선택 합니다.](media/custom-keyword/custom-kws-portal-project-list.png)

1. 새 키워드 모델을 시작 하려면 **모델 학습**을 클릭 합니다.

1. 키워드 모델의 **이름과** **설명** (선택 사항)을 입력 하 고 원하는 **키워드** 를 입력 한 후 **다음**을 클릭 합니다. 효과적인 키워드를 선택 하는 데 도움이 되는 몇 가지 [지침이](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) 있습니다.

    ![키워드 입력](media/custom-keyword/custom-kws-portal-new-model.png)

1. 이제 포털에서 키워드에 대 한 후보 발음을 만듭니다. 재생 단추를 클릭 하 여 각 후보를 수신 하 고 잘못 된 발음 옆의 검사를 제거 합니다. 올바른 발음만 선택 된 후에는 **학습** 을 클릭 하 여 키워드 생성을 시작 합니다. 

    ![키워드 검토](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 모델을 생성 하는 데 최대 10 분이 소요 될 수 있습니다. 모델이 완료 되 면 키워드 목록이 **처리** 에서 **성공** 으로 변경 됩니다. 그런 다음 파일을 다운로드할 수 있습니다.

    ![키워드 검토](media/custom-keyword/custom-kws-portal-download-model.png)

1. .zip 파일을 컴퓨터에 저장합니다. 사용자 지정 키워드를 장치에 배포 하려면이 파일이 필요 합니다.

## <a name="next-steps"></a>다음 단계

[음성 장치 SDK 퀵 스타트](https://aka.ms/sdsdk-quickstart)를 사용 하 여 사용자 지정 키워드를 테스트 합니다.
