---
title: 릴리스 정보 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 사용자 지정 비전 팀의 새로운 릴리스에 대한 최신 정보를 가져옵니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647569"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service 릴리스 정보

## <a name="may-2-2019-and-may-10-2019"></a>2019년 5월 2일 및 2019년 5월 10일

- 버그 수정 및 백엔드 개선 사항

## <a name="may-23-2019"></a>2019년 5월 23일

- Azure 구독과 관련된 향상된 포털 UX 환경을 개선하여 Azure 디렉터리를 더 쉽게 선택할 수 있습니다.

## <a name="april-18-2019"></a>2019년 4월 18일 

- 비전 AI 개발 키트에 오브젝트 감지 내보내기가 추가되었습니다.
- 프로젝트 검색을 포함한 UI 조정.

## <a name="april-3-2019"></a>2019년 4월 3일

- 이미지당 경계 상자 수 제한을 200개로 늘렸습니다. 
- TensorFlow로 내보낸 모델에 대한 실질적인 성능 업데이트를 포함한 버그 수정. 

## <a name="march-26-2019"></a>2019년 3월 26일

- 사용자 지정 비전 서비스가 Azure에서 일반 공급에 들어갔습니다!
- 특히 까다로운 데이터 집합 및 세분화된 분류에서 향상된 성능을 위해 새로운 기계 학습 백엔드와 고급 교육 기능이 추가되었습니다. 고급 교육을 사용하면 교육을 위한 계산 시간 예산을 지정할 수 있으며 사용자 지정 비전은 최상의 교육 및 보강 설정을 실험적으로 식별합니다. 빠른 반복을 위해 기존 빠른 교육을 계속 사용할 수 있습니다.
- 3.0 API를 도입했습니다. 2019년 10월 1일에 3.0 이전 API의 사용 중단을 발표했습니다. [.Net,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial) [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [노드,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial) [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial) [또는](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) 시작 방법에 대한 예제에 대한 설명서 퀵스타트를 참조하십시오.
- "기본 반복"을 3.0 API에서 게시/게시 취소로 대체했습니다.
- 새 모델 내보내기 대상이 추가되었습니다. Dockerfile 내보내기는 라즈베리 파이에 대 한 ARM을 지원 하도록 업그레이드 되었습니다 3. [비전 AI 개발 키트에](https://visionaidevkit.com/)수출 지원이 추가되었습니다.
- 프로젝트당 태그 한도가 S0 계층의 경우 500으로 증가했습니다. 프로젝트당 이미지 제한이 S0 계층의 경우 100,000개로 증가했습니다.
- 성인 도메인이 삭제되었습니다. 대신 일반 도메인을 권장합니다.
- 일반 [공급에](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 대한 가격 발표.  

## <a name="february-25-2019"></a>2019년 2월 25일

- 사용자 지정 비전이 Azure 공개 미리 보기로의 이동이 거의 완료됨에 따라 제한된 평가판 프로젝트(Azure 리소스와 연결되지 않은 프로젝트)의 종료를 발표했습니다. 2019년 3월 25일부터 CustomVision.ai 사이트는 무료 사용자 지정 비전 리소스와 같은 Azure 리소스와 관련된 프로젝트 보기만 지원합니다. 2019년 10월 1일까지 사용자 지정 비전 API를 통해 기존 제한된 평가판 프로젝트에 계속 액세스할 수 있습니다. 이렇게 하면 사용자 지정 비전으로 작성한 모든 앱에 대한 API 키를 업데이트할 수 있습니다. 2019년 10월 1일 이후에Azure로 이동하지 않은 제한된 평가판 프로젝트는 삭제됩니다.

## <a name="january-22-2019"></a>2019년 1월 22일

- 새 Azure 지역에 대한 지원이 추가되었습니다: 미국 서부 2, 미국 동부, 미국 동부 2, 유럽 서부, 북유럽, 동남아시아, 호주 동부, 인도 중부, 영국 남부, 일본 동부 및 미국 중북부. 미국 중남부에 대한 지원은 계속됩니다.

## <a name="december-12-2018"></a>2018년 12월 12일

- 개체 감지 모델(도입된 개체 감지 컴팩트 도메인) 내보내기를 지원합니다.
- 향상된 화면 판독기 및 키보드 탐색 지원에 대한 여러 액세스 가능성 문제를 해결했습니다.
- 이미지 뷰어 및 빠른 태그 지정을 위한 향상된 개체 감지 태그 지정 환경에 대한 UX 업데이트입니다.  
- 향상된 품질의 개체 감지를 위한 개체 감지 도메인의 기본 모델을 업데이트했습니다.
- 버그 수정.

## <a name="november-6-2018"></a>2018년 11월 6일

- 개체 감지에서 로고 도메인에 대한 지원을 추가했습니다.

## <a name="october-9-2018"></a>2018년 10월 9일

- 개체 감지는 유료 미리 보기로 전환됩니다. 이제 Azure 리소스를 사용하여 개체 감지 프로젝트를 만들 수 있습니다.
- 제한된 평가판 프로젝트를 Azure 리소스 연결 프로젝트(F0 또는 S0) 제품의 설정 페이지에서 찾을 수 있습니다.  
- Windows ML의 Windows 2018년 10월 업데이트 버전을 지원하도록 ONNX 1.2로의 내보내기가 추가되었습니다.
특수 문자로 ONNX 내보내기를 포함한 버그 수정.

## <a name="august-14-2018"></a>2018년 8월 14일

- 사용자에게 프로젝트 학습을 안내하는 "시작" 위젯이 customvision.ai 사이트 에 추가되었습니다.
- 다중 레이블 프로젝트에 유용하도록 기계 학습 파이프라인이 추가로 개선되었습니다(새 손실 계층).

## <a name="june-28-2018"></a>2018년 6월 28일

- 버그 수정 & 백 엔드 개선.
- 이미지에 레이블이 정확히 하나있는 프로젝트에 대해 다중 클래스 분류를 사용하도록 설정했습니다. 다중 클래스 모드에 대한 예측에서 확률은 1로 합산됩니다(모든 이미지는 지정된 태그로 분류됩니다).

## <a name="june-13-2018"></a>2018년 6월 13일

- 사용 편의성 및 접근성에 초점을 맞춘 UX 새로 고침 기능이 제공됩니다.
- 많은 수의 태그가 있는 다중 레이블 프로젝트에 유용하도록 기계 학습 파이프라인이 개선되었습니다.
- TensorFlow 내보내기의 버그가 수정되었습니다. 내보낸 모델 버전 관리가 지원되므로 반복을 2번 이상 내보낼 수 있습니다.

## <a name="may-7-2018"></a>2018년 5월 7일

- 제한된 평가판 프로젝트에 대한 미리 보기 개체 감지 기능이 도입되었습니다.
- 2.0 API로 업그레이드
- S0 계층이 태그 250개 이미지 50,000까지 확장되었습니다.
- 이미지 분류 프로젝트용 기계 학습 파이프 라인의 백 엔드가 크게 개선되었습니다. 2018년 4월 27일 이후에 학습된 프로젝트에는 이 업데이트가 도움이 됩니다.
- Windows ML에서 사용할 수 있도록 ONNX에 모델 내보내기가 추가되었습니다.
- Dockerfile에 모델 내보내기가 추가되었습니다. 따라서 아티팩트를 다운로드하여 자신만의 Windows 또는 Linux 컨테이너(예: DockerFile, TensorFlow 모델 및 서비스 코드)를 빌드할 수 있습니다.
- 일반(컴팩트) 및 랜드마크(컴팩트) 도메인에서 TensorFlow로 내보낸 새로 학습된 모델의 경우 모든 프로젝트의 일관성을 위해 [평균값이 이제 (0,0,0)입니다](https://github.com/azure-samples/cognitive-services-android-customvision-sample).

## <a name="march-1-2018"></a>2018년 3월 1일

- 유료 미리 보기를 입력하고 Azure 포털에 온보답게 했습니다. 이제 F0(무료) 또는 S0(표준) 계층으로 Azure 리소스에 프로젝트를 연결할 수 있습니다. 최대 태그 100개 이미지 25,000개까지 허용하는 S0 계층 프로젝트가 도입되었습니다.
- 백 엔드가 기계 학습 파이프 라인/정규화 매개 변수로 변경됩니다. 따라서 확률 임계값을 조정할 때 고객이 정밀도-회수 상충 관계를 더 잘 제어할 수 있습니다. 이러한 변화의 일환으로, CustomVision.ai 포털의 기본 확률 임계값이 50%로 설정되었습니다.

## <a name="december-19-2017"></a>2017년 12월 19일

- 안드로이드로의 내보내기 (TensorFlow)는 이전에 출시 된 iOS (CoreML)에 대한 수출 외에도 추가되었습니다. 이렇게 하면 학습된 소형 모델의 내보내기를 응용 프로그램에서 오프라인으로 실행할 수 있습니다.
- 소매 및 랜드마크 “컴팩트” 도메인이 추가되어 이러한 도메인에 대한 모델 내보내기가 가능합니다.
- 버전 [1.2 교육 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 및 [1.1 예측 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)가 릴리스되었습니다. 업데이트된 API는 모델 내보내기, "예측"에 이미지를 저장하지 않는 새로운 예측 작업을 지원하며 교육 API에 일괄 처리 작업을 도입했습니다.
- 반복 학습에 사용된 도메인을 확인하는 기능을 비롯한 UX가 조정되었습니다.
- [C# SDK 및 샘플](https://github.com/Microsoft/Cognitive-CustomVision-Windows)이 업데이트되었습니다.