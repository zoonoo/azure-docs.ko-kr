---
title: 사용자 지정 키워드 만들기 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 기기는 항상 키워드(또는 구)를 수신 대기하고 있습니다. 사용자가 키워드를 말하면 장치는 사용자가 말하기를 멈출 때까지 모든 후속 오디오를 클라우드로 보냅니다. 키워드를 사용자 지정하면 기기를 차별화하고 브랜딩을 강화할 수 있습니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717012"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>스피치 스튜디오를 사용하여 사용자 지정 키워드 만들기

기기는 항상 키워드(또는 구)를 수신 대기하고 있습니다. 예를 들어 " 안녕 마이크로 소프트 코타나"는 마이크로 소프트 코타나 도우미의 키워드입니다. 사용자가 키워드를 말하면 장치는 사용자가 말하기를 멈출 때까지 모든 후속 오디오를 클라우드로 보냅니다. 키워드를 사용자 지정하면 기기를 차별화하고 브랜딩을 강화할 수 있습니다.

이 문서에서는 기기에 대한 사용자 지정 키워드를 만드는 방법을 배웁니다.

## <a name="create-your-keyword"></a>키워드 만들기

사용자 지정 키워드를 사용하려면 음성 [스튜디오](https://aka.ms/sdsdk-speechportal)의 사용자 지정 [키워드](https://aka.ms/sdsdk-wakewordportal) 페이지를 사용하여 키워드를 만들어야 합니다. 키워드를 제공하면 장치에 배포하는 파일이 생성됩니다.

1. [음성 스튜디오로](https://aka.ms/sdsdk-speechportal) 이동하여 **로그인하거나** 아직 음성 구독이 없는 경우 [**구독 만들기를**](https://go.microsoft.com/fwlink/?linkid=2086754)선택합니다.

1. 사용자 [지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지에서 **새 프로젝트를**만듭니다. 

1. **이름**, 선택적 **설명을**입력하고 언어를 선택합니다. 언어당 하나의 프로젝트가 필요하며 지원은 현재 en-US 언어로 제한됩니다.

    ![키워드 프로젝트 설명](media/custom-keyword/custom-kws-portal-new-project.png)

1. 목록에서 프로젝트를 선택합니다. 

    ![키워드 프로젝트 선택](media/custom-keyword/custom-kws-portal-project-list.png)

1. 새 키워드 모델을 시작하려면 **Train 모델을**클릭합니다.

1. 키워드 모델의 **이름을** 입력하고 선택 한 **키워드에** **설명** 및 입력을 선택하고 **다음을**클릭합니다. 효과적인 키워드를 선택하는 데 도움이 되는 몇 가지 [지침이](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) 있습니다.

    ![키워드 입력](media/custom-keyword/custom-kws-portal-new-model.png)

1. 이제 포털에서 키워드에 대한 후보 발음을 만듭니다. 재생 버튼을 클릭하여 각 후보를 듣고 잘못된 발음 옆에 있는 체크를 제거합니다. 좋은 발음만 확인되면 **Train을** 클릭하여 키워드 생성을 시작합니다. 

    ![키워드 검토](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 모델이 생성되려면 최대 30분이 걸릴 수 있습니다. 모델이 완료되면 키워드 목록이 **처리에서** **성공으로** 변경됩니다. 그런 다음 파일을 다운로드할 수 있습니다.

    ![키워드 검토](media/custom-keyword/custom-kws-portal-download-model.png)

1. .zip 파일을 컴퓨터에 저장합니다. 사용자 지정 키워드를 장치에 배포하려면 이 파일이 필요합니다.

## <a name="next-steps"></a>다음 단계

[음성 장치 SDK 빠른 시작으로](https://aka.ms/sdsdk-quickstart)사용자 정의 키워드를 테스트합니다.
