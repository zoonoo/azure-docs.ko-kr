---
title: 릴리스 정보 - Custom Vision Service
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: b712f47fe9272e0ae6ccb9ab9847462729434698
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894414"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service 릴리스 정보

## <a name="january-22-2019"></a>2019년 1월 22일
- 다음과 같은 새 Azure 지역에 대한 지원이 추가되었습니다. 미국 서부 2, 미국 동부, 미국 동부 2, 유럽 서부, 유럽 북부, 동남 아시아, 오스트레일리아 동부, 인도 중부, 영국 남부, 일본 동부 및 미국 중북부. 미국 중남부에 대한 지원은 계속됩니다. 

## <a name="december-12-2018"></a>2018년 12월 12일
- 개체 감지 모델(도입된 개체 감지 컴팩트 도메인) 내보내기를 지원합니다.
- 향상된 화면 판독기 및 키보드 탐색 지원에 대한 여러 액세스 가능성 문제를 해결했습니다. 
- 이미지 뷰어 및 빠른 태그 지정을 위한 향상된 개체 감지 태그 지정 환경에 대한 UX 업데이트입니다.  
- 향상된 품질의 개체 감지를 위한 개체 감지 도메인의 기본 모델을 업데이트했습니다. 
- 버그 수정입니다.

## <a name="november-6-2018"></a>2018년 11월 6일
- 개체 감지에서 로고 도메인에 대한 지원을 추가했습니다.

## <a name="october-9-2018"></a>2018년 10월 9일
- 개체 감지는 유료 미리 보기로 전환됩니다. 이제 Azure 리소스를 사용하여 개체 감지 프로젝트를 만들 수 있습니다.
- 제한된 평가판 프로젝트를 Azure 리소스 연결 프로젝트(F0 또는 S0) 링크로 보다 쉽게 업그레이드할 수 있도록 웹 사이트에 “Azure에 이동” 기능이 추가되었습니다. 제품의 설정 페이지에서 이 기능을 찾을 수 있습니다.  
- Windows ML의 Windows 2018년 10월 업데이트 버전을 지원하도록 ONNX 1.2로의 내보내기가 추가되었습니다.
버그 수정(특수 문자를 사용한 ONNX 내보내기 관련 버그 수정 포함) 

## <a name="august-14-2018"></a>2018년 8월 14일
- 사용자에게 프로젝트 학습을 안내하는 "시작" 위젯이 customvision.ai 사이트 에 추가되었습니다. 
- 다중 레이블 프로젝트에 유용하도록 기계 학습 파이프라인이 추가로 개선되었습니다(새 손실 계층).

## <a name="june-28-2018"></a>2018년 6월 28일
- 버그 수정 및 백 엔드 기능이 개선되었습니다.
- 이미지에 정확히 1개의 레이블만 있는 프로젝트에 대해 다중 클래스 분류가 사용되도록 설정되었습니다. 다중 클래스 모드에 대한 예측에서 확률을 더하면 1이 됩니다(모든 이미지가 지정된 태그로 분류됨).

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
- 유료 미리 보기로 전환되고 Azure Portal에 온보딩되었습니다. 이제 F0(무료) 또는 S0(표준) 계층으로 Azure 리소스에 프로젝트를 연결할 수 있습니다. 최대 태그 100개 이미지 25,000개까지 허용하는 S0 계층 프로젝트가 도입되었습니다. 
- 백 엔드가 기계 학습 파이프 라인/정규화 매개 변수로 변경됩니다. 따라서 확률 임계값을 조정할 때 고객이 정밀도-회수 상충 관계를 더 잘 제어할 수 있습니다. 이러한 변화의 일환으로, CustomVision.ai 포털의 기본 확률 임계값이 50%로 설정되었습니다.

## <a name="december-19-2017"></a>2017년 12월 19일

- 이전에 릴리스한 iOS로 내보내기(CoreML)외에 Android로 내보내기(TensorFlow)가 추가되었습니다. 따라서 학습된 컴팩트 모델을 내보내서 애플리케이션에서 오프라인으로 실행할 수 있습니다.
- 소매 및 랜드마크 “컴팩트” 도메인이 추가되어 이러한 도메인에 대한 모델 내보내기가 가능합니다.
- 버전 [1.2 교육 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 및 [1.1 예측 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)가 릴리스되었습니다. 업데이트된 API는 모델 내보내기, "예측"에 이미지를 저장하지 않는 새로운 예측 작업을 지원하며 교육 API에 일괄 처리 작업을 도입했습니다.
- 반복 학습에 사용된 도메인을 확인하는 기능을 비롯한 UX가 조정되었습니다.
- [C# SDK 및 샘플](https://github.com/Microsoft/Cognitive-CustomVision-Windows)이 업데이트되었습니다.

