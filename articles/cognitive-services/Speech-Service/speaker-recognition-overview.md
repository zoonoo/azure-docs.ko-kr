---
title: Speaker Recognition 개요-음성 서비스
titleSuffix: Azure Cognitive Services
description: Speaker Recognition는 음성 biometry을 사용 하 여 고유한 음성 특성에 따라 스피커를 확인 하 고 식별 하는 알고리즘을 제공 합니다. 화자 인식은 "누가 말하고 있나요?"라는 질문에 대답하는 데 사용됩니다. 이 문서는 Speaker Recognition 서비스의 이점 및 기능에 대 한 개요입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: 스피커 인식, 음성 생체 측정
ms.openlocfilehash: 2c5b73b93c22ef27d7b68455f5e1e5108f25c984
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397262"
---
# <a name="what-is-speaker-recognition"></a>Speaker Recognition이란?

Speaker Recognition 서비스는 음성 biometry을 사용 하 여 고유한 음성 특성에 따라 스피커를 확인 하 고 식별 하는 알고리즘을 제공 합니다. 화자 인식은 "누가 말하고 있나요?"라는 질문에 대답하는 데 사용됩니다. 스피커 음성의 고유한 특성을 기반으로 등록 프로필을 만드는 단일 스피커에 대 한 오디오 학습 데이터를 제공 합니다. 그런 다음이 프로필에 대 한 오디오 음성 샘플을 교차 확인 하 여 스피커가 동일한 사람 (스피커 확인) 인지 확인 하거나 등록 된 스피커 프로필 *그룹* 에 대 한 오디오 음성 샘플을 교차 확인 하 여 그룹의 프로필 (스피커 식별)과 일치 하는지 확인할 수 있습니다. 반면, [스피커 Diarization](batch-transcription.md#speaker-separation-diarization) 는 일괄 처리 작업에서 스피커로 오디오 세그먼트를 그룹화 합니다.

## <a name="speaker-verification"></a>화자 검증

Speaker Verification는 암호 또는 자유 형식 음성 입력으로 등록 된 발표자 id를 확인 하는 프로세스를 간소화 합니다. 이를 사용 하 여 콜 센터의 고객 id 검증부터 연락처 없는 기능 액세스까지 광범위 한 솔루션에서 보안, 원활한 고객의 의견을 확인할 수 있습니다.

### <a name="how-does-speaker-verification-work"></a>Speaker Verification 작동 방법

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="순서도를 Speaker Verification 합니다.":::

스피커 확인은 텍스트 종속 또는 텍스트 독립적 일 수 있습니다. **텍스트 종속** 확인은 스피커가 등록 및 확인 단계에서 사용할 동일한 암호를 선택 해야 함을 의미 합니다. **텍스트 독립적인** 확인은 스피커가 등록 및 확인 구에 일상적인 언어로 말할 수 있음을 의미 합니다.

**텍스트 종속** 확인의 경우 미리 정의 된 구 집합의 암호를 말하여 스피커의 음성이 등록 됩니다. 오디오 녹음에서 음성 기능을 추출 하 여 고유한 음성 서명을 구성 하 고, 선택한 암호도 인식 합니다. 음성 서명 및 암호를 함께 사용 하 여 스피커를 확인 합니다. 

**텍스트 독립적** 확인은 유사성의 점수를 매길 음성 기능만 추출 하기 때문에 발표자가 등록 중에 표시 되는 항목 또는 오디오 샘플에 대 한 제한이 없습니다. 

Api는 오디오가 live person에서 온 것인지 아니면 등록 된 강연자의 모조/기록 인지를 확인 하기 위한 것이 아닙니다. 

## <a name="speaker-identification"></a>화자 식별

Speaker Identification는 등록 된 스피커 그룹 내에서 알 수 없는 스피커의 id를 확인 하는 데 사용 됩니다. Speaker Identification를 사용 하면 개별 스피커에 음성 특성을 지정할 수 있으며 여러 스피커가 있는 시나리오에서 다음과 같은 값의 잠금을 해제할 수 있습니다.

* 원격 회의 생산성에 대 한 지원 솔루션 
* 다중 사용자 장치 개인 설정 빌드

### <a name="how-does-speaker-identification-work"></a>Speaker Identification 작동 방법

발표자 식별에 대 한 등록은 **텍스트 독립적**이므로 발표자가 오디오에 표시 하는 내용에 대 한 제한이 없습니다. Speaker Verification와 마찬가지로 등록 단계에서 스피커 음성이 기록 되 고 음성 기능을 추출 하 여 고유한 음성 서명을 구성 합니다. 식별 단계에서 입력 음성 샘플은 지정 된 등록 된 음성 목록 (각 요청에서 최대 50)과 비교 됩니다.

## <a name="data-security-and-privacy"></a>데이터 보안 및 개인 정보

스피커 등록 데이터는 등록을 위한 음성 오디오 및 음성 서명 기능을 포함 하 여 보안 시스템에 저장 됩니다. 등록을 위한 음성 오디오는 알고리즘이 업그레이드 된 경우에만 사용 되며 기능을 다시 추출 해야 합니다. 서비스는 인식 단계에서 서비스로 전송 되는 음성 녹음 또는 추출 된 음성 기능을 유지 하지 않습니다. 

데이터를 보존 해야 하는 기간을 제어 합니다. API 호출을 통해 개별 스피커에 대 한 등록 데이터를 만들고, 업데이트 하 고, 삭제할 수 있습니다. 구독이 삭제되면 구독과 연결된 모든 화자 등록 데이터도 삭제됩니다. 

모든 Cognitive Services 리소스와 마찬가지로 Speaker Recognition 서비스를 사용 하는 개발자는 고객 데이터에 대 한 Microsoft의 정책을 인식 해야 합니다. 사용자가 Speaker Recognition에 대 한 적절 한 사용 권한을 받았는지 확인 해야 합니다. 자세한 내용은 Microsoft 보안 센터의 [Cognitive Services 페이지](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)를 참조 하세요   . 

## <a name="common-questions-and-solutions"></a>일반적인 질문 및 해결 방법

| 질문 | 해결 방법 |
|---------|----------|
| 어떤 시나리오를 사용할 수 Speaker Recognition 있나요? | 콜 센터 고객 검증, 음성 기반 환자 체크 인, 모임 기록, 다중 사용자 장치 개인 설정|
| 식별과 확인의 차이점은 무엇 인가요? | 식별은 발표자 그룹의 멤버가 말하는 멤버를 검색 하는 프로세스입니다. 확인은 스피커가 알려진 음성 또는 **등록** 된 음성과 일치 하는지 확인 하는 행위입니다.|
| 텍스트 종속 및 텍스트 독립적인 확인의 차이점은 무엇 인가요? | 텍스트 종속 확인에는 등록과 인식 모두를 위한 특정 구가 필요 합니다. 텍스트 독립적 인증에는 등록을 위한 긴 음성 샘플이 필요 하지만 인식 하는 동안을 비롯 하 여 모든 것을 말할 수 있습니다.|
| 어떤 언어가 지원되나요? | 영어, 프랑스어, 스페인어, 중국어, 독일어, 이탈리아어, 일본어 및 포르투갈어 |
| 지원 되는 Azure 지역은 무엇 인가요? | Speaker Recognition는 미리 보기 서비스 이며 현재 미국 서 부 지역 에서만 사용할 수 있습니다.|
| 지원 되는 오디오 형식은 무엇입니까? | Mono 16 비트, 16kHz PCM-인코드된 WAV |
| **수락** 및 **거부** 응답이 정확 하지 않습니다. 임계값을 조정 하는 방법 | 최적 임계값은 시나리오에 따라 달라 지기 때문에 API는 기본 임계값 0.5에 따라 간단히 "수락" 또는 "거부"를 결정 합니다. 고급 사용자는 기본 결정을 재정의 하 고 사용자의 시나리오에 따라 결과를 미세 조정 하는 것이 좋습니다. |
| 하나의 스피커를 여러 번 등록할 수 있나요? | 예, 텍스트 종속 확인의 경우 최대 50 번 스피커를 등록할 수 있습니다. 텍스트 독립적인 확인 또는 발표자 id의 경우 최대 300 초까지 오디오를 등록할 수 있습니다. |
| Azure에 저장 되는 데이터는 무엇 인가요? | 등록 오디오는 음성 프로필을 [삭제할](speaker-recognition-basics.md#deleting-voice-profile-enrollments)때까지 서비스에 저장 됩니다. 인식 오디오 샘플은 유지 되거나 저장 되지 않습니다. |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> * 응용 프로그램에서 사용할 수 있는 일반적인 디자인 패턴의 실행에 대 한 Speaker Recognition [기본 문서](speaker-recognition-basics.md) 를 완료 합니다.
> * 텍스트 독립적인 스피커 확인에 대 한 [비디오 자습서](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) 를 참조 하세요.
