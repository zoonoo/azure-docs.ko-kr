---
title: Microsoft Translator Hub 작업 영역 및 프로젝트를 마이그레이션하려면? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 허브 작업 영역 및 프로젝트를 Custom Translator로 마이그레이션합니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 378baad0735238dc0921e5e78e2a27b3ae907e19
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628045"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>허브 작업 영역 및 프로젝트를 Custom Translator로 마이그레이션

[Microsoft Translator Hub](https://hub.microsofttranslator.com/) 작업 영역 및 프로젝트를 Custom Translator로 마이그레이션할 수 있습니다. 마이그레이션은 허브에서 시작됩니다.


다음과 같은 항목이 프로세스 동안 마이그레이션됩니다.

1.  프로젝트 정의.

2.  학습 정의는 Custom Translator에서 새 모델 정의를 만드는 데 사용됩니다.

3.  학습 내에서 사용되는 병렬 및 단일 언어 파일은 모두 Custom Translator에서 새 문서로 마이그레이션됩니다.

4.  자동 생성된 시스템 테스트 및 튜닝 데이터는 내보내기되어 Custom Translator의 새 문서로 만들어집니다.

배포된 모든 학습에서 Custom Translator는 비용 없이 모델을 학습합니다. 수동으로 해당 학습을 배포하는 옵션이 있습니다.

>[!Note]
>학습에 성공하려면 Custom Translator에 최소 10,000개의 추출된 문장이 필요합니다. 추출된 문장 수가 [제안된 최소 수](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences)보다 적으면 Custom Translator는 학습을 수행할 수 없습니다.

배포되지 않은 모든 성공적인 학습에서 이러한 학습은 Custom Translator에서 초안으로 마이그레이션됩니다.

## <a name="find-custom-translator-workspace-id"></a>Custom Translator 작업 영역 ID 찾기

[Microsoft Translator Hub](https://hub.microsofttranslator.com/) 작업 영역을 마이그레이션하려면 Custom Translator의 대상 작업 영역 ID가 필요합니다. Custom Translator의 대상 작업 영역은 모든 허브 작업 영역 및 프로젝트를 마이그레이션할 위치입니다.

Custom Translator 설정 페이지에서 대상 작업 영역 ID를 찾을 수 있습니다. 

1.  Custom Translator 포털에서 “설정” 페이지로 이동합니다.

2.  기본 정보 섹션에서 작업 영역 ID를 찾을 수 있습니다.

    ![대상 작업 영역 ID를 찾는 방법](media/how-to/how-to-find-destination-ws-id.png)

3. 마이그레이션 프로세스 중에 참조할 대상 작업 공간 ID를 적어둡니다.

## <a name="migrate-workspace"></a>작업 영역 마이그레이션

Custom Translator에 대한 전체 허브 작업 영역을 마이그레이션하는 경우 프로젝트, 문서 및 학습이 Custom Translator로 마이그레이션됩니다. 마이그레이션 전에 배포된 학습만 마이그레이션할지 아니면 성공적인 학습을 모두 마이그레이션할지 선택해야 합니다.

작업 영역을 마이그레이션하려면

1.  Microsoft Translator Hub에 로그인합니다.

2.  “설정” 페이지로 이동합니다.

3.  “설정” 페이지에서 “Custom Translator로 작업 영역 데이터 마이그레이션”을 클릭합니다.

    ![허브에서 마이그레이션하는 방법](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  다음 페이지에서 다음과 같은 두 옵션 중 하나를 선택합니다.

    a.  배포된 학습만: 이 옵션을 선택하면 배포된 시스템 및 관련된 문서만 마이그레이션됩니다.

    b.  모든 성공적인 학습: 이 옵션을 선택하면 성공적인 학습 및 관련된 문서가 모두 마이그레이션됩니다.

    다.  Custom Translator의 대상 작업 영역 ID를 입력합니다.

    ![허브에서 마이그레이션하는 방법](media/how-to/how-to-migrate-from-hub-screen.png)

5.  요청 제출을 클릭합니다.

## <a name="migrate-project"></a>프로젝트 마이그레이션

선택적으로 프로젝트를 마이그레이션하려는 경우 Microsoft Translator Hub는 해당 기능을 제공합니다.

프로젝트를 마이그레이션하려면

1.  Microsoft Translator Hub에 로그인합니다.

2.  “프로젝트” 페이지로 이동합니다.

3.  해당 프로젝트에서 “마이그레이션” 링크를 클릭합니다.

    ![허브에서 마이그레이션하는 방법](media/how-to/how-to-migrate-from-hub.png)

4.  다음 페이지에서 다음과 같은 두 옵션 중 하나를 선택합니다.

    a.  배포된 학습만: 이 옵션을 선택하면 배포된 시스템 및 관련된 문서만 마이그레이션됩니다. 

    b.  모든 성공적인 학습: 이 옵션을 선택하면 성공적인 학습 및 관련된 문서가 모두 마이그레이션됩니다.

    다.  Custom Translator의 대상 작업 영역 ID를 입력합니다.

    ![허브에서 마이그레이션하는 방법](media/how-to/how-to-migrate-from-hub-screen.png)

5.  “요청 제출”을 클릭합니다.

## <a name="migration-history"></a>마이그레이션 기록

허브에서 작업 영역/프로젝트 마이그레이션을 요청한 경우 Custom Translator 설정 페이지에서 마이그레이션 기록을 찾을 수 있습니다. 

마이그레이션 기록을 보려면 다음 단계를 수행합니다.

1.  Custom Translator 포털에서 “설정” 페이지로 이동합니다.

2.  설정 페이지의 마이그레이션 기록 섹션에서 마이그레이션 기록을 클릭합니다.

    ![마이그레이션 기록](media/how-to/how-to-migration-history.png)

마이그레이션 기록 페이지에는 요청한 모든 마이그레이션에 대한 요약으로 다음 정보가 표시됩니다.

1.  마이그레이션한 사람: 이 마이그레이션 요청을 제출한 사용자의 이름 및 이메일

2.  마이그레이션한 날짜: 마이그레이션의 날짜 및 타임스탬프

3.  프로젝트: 마이그레이션에 대해 요청된 프로젝트 수 v/s 성공적으로 마이그레이션된 프로젝트 수

4.  학습: 마이그레이션에 대해 요청된 학습 수 v/s 성공적으로 마이그레이션된 학습 수

5.  문서: 마이그레이션에 대해 요청된 문서 수 v/s 성공적으로 마이그레이션된 문서 수

    ![마이그레이션 기록 세부 정보](media/how-to/how-to-migration-history-details.png)

프로젝트, 학습 및 문서에 대한 더 자세한 마이그레이션 보고서를 보려면 CSV로 세부 정보 내보내기 옵션이 있습니다.

>[!Note]
>마이그레이션은 NMT 언어가 있는 언어 쌍에만 지원됩니다. 현재 [지원되는 NMT 언어](https://www.microsoft.com/translator/business/languages/)의 목록을 확인하세요. NMT 언어가 존재하지 않는 언어 쌍의 경우 데이터가 허브에서 Custom Translator로 이동되지만 해당 언어 쌍에 대해 학습을 수행할 수는 없습니다.

## <a name="next-steps"></a>다음 단계

- [모델을 학습합니다](how-to-train-model.md).
- [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)을 통해 배포된 사용자 지정 번역 모델 사용을 시작합니다.