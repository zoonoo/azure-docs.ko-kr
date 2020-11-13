---
title: 릴리스 정보 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Custom Vision 팀에서 새 릴리스에 대 한 최신 정보를 확인 하세요.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: ccf742e5ff612816cae1056ef7bf0e64fc9dbb5b
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616470"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service 릴리스 정보

## <a name="may-2-2019-and-may-10-2019"></a>5 월 2 일, 2019 년 5 월 10 일 2019

- 버그 수정 및 백 엔드 기능 향상

## <a name="may-23-2019"></a>2019년 5월 23일

- Azure 구독과 관련 된 포털 UX 환경을 개선 하 여 Azure 디렉터리를 쉽게 선택할 수 있습니다.

## <a name="april-18-2019"></a>2019년 4월 18일 

- 비전 AI Dev Kit에 대 한 개체 검색 내보내기를 추가 했습니다.
- 프로젝트 검색을 포함 하는 UI 조정

## <a name="april-3-2019"></a>2019 년 4 월 3 일

- 이미지 당 경계 상자 수에 대 한 제한이 200로 증가 했습니다. 
- TensorFlow로 내보낸 모델에 대 한 상당한 성능 업데이트를 포함 하는 버그 수정 

## <a name="march-26-2019"></a>2019년 3월 26일

- Custom Vision Service Azure에서 일반 공급으로 전환 되었습니다.
- 성능 향상을 위해 새 machine learning 백 엔드가 포함 된 고급 학습 기능이 추가 되었습니다. 특히 데이터 집합 및 세분화 된 분류의 경우 고급 교육을 통해 교육에 대 한 계산 시간 예산을 지정 Custom Vision 하 고 experimentally는 최상의 학습 및 확대 설정을 식별할 수 있습니다. 빠른 반복의 경우 기존 빠른 학습을 계속 사용할 수 있습니다.
- 3.0 Api가 도입 되었습니다. 발표 된는 2019 년 10 월 1 일 3.0 Api의 사용 중단을 발표 했습니다. 시작 하는 방법에 대 한 예제는 [.net](./quickstarts/image-classification.md), [Python](./quickstarts/image-classification.md), [Node](./quickstarts/image-classification.md), [Java](./quickstarts/image-classification.md)또는 [Go](./quickstarts/image-classification.md) 에 대 한 설명서를 참조 하세요.
- 3.0 Api에서 "기본 반복"이 게시/게시 취소로 바뀌었습니다.
- 새 모델 내보내기 대상이 추가 되었습니다. Dockerfile 내보내기가 Raspberry Pi 3의 ARM을 지원 하도록 업그레이드 되었습니다. 수출 지원이 [비전 AI Dev Kit](https://visionaidevkit.com/)에 추가 되었습니다.
- S0 계층의 경우 프로젝트 당 500에 대 한 태그 제한이 증가 했습니다. S0 계층의 경우 10만에 대 한 프로젝트당 이미지 한도가 증가 합니다.
- 성인 도메인을 제거 했습니다. 대신 일반 도메인을 권장 합니다.
- 일반 공급 [가격](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 에 대 한 발표  

## <a name="february-25-2019"></a>2019년 2월 25일

- Azure 공개 미리 보기로의 이동이 완료 Custom Vision 하는 제한 된 평가판 프로젝트 (Azure 리소스와 연결 되지 않은 프로젝트)의 끝을 발표 했습니다. 2019 년 3 월 25 일부 터 CustomVision.ai 사이트는 무료 Custom Vision 리소스와 같은 Azure 리소스와 관련 된 프로젝트 보기만 지원 합니다. 2019 년 10 월 1 일부 터 Custom Vision Api를 통해 기존의 제한 된 평가판 프로젝트에 계속 액세스할 수 있습니다. 이렇게 하면 Custom Vision를 사용 하 여 작성 한 모든 앱에 대 한 API 키를 업데이트할 수 있습니다. 2019 년 10 월 1 일 이후에는 Azure로 이동 하지 않은 제한 된 평가판 프로젝트가 삭제 됩니다.

## <a name="january-22-2019"></a>2019년 1월 22일

- 새 Azure 지역에 대 한 지원 추가: 미국 서 부 2, 미국 동부, 미국 동부 2, 유럽 서부, 유럽, 동남 아시아, 오스트레일리아 동부, 인도 중부, 영국 남부, 일본 동부 및 미국 중 북부 미국 중남부에 대한 지원은 계속됩니다.

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
- 제한된 평가판 프로젝트를 Azure 리소스 연결 프로젝트 (F0 또는 S0) 제품에 대 한 설정 페이지에서이를 찾을 수 있습니다.  
- Windows ML의 Windows 2018년 10월 업데이트 버전을 지원하도록 ONNX 1.2로의 내보내기가 추가되었습니다.
특수 문자를 포함 하는 ONNX 내보내기를 포함 하는 버그 수정

## <a name="august-14-2018"></a>2018년 8월 14일

- 사용자에게 프로젝트 학습을 안내하는 "시작" 위젯이 customvision.ai 사이트 에 추가되었습니다.
- 다중 레이블 프로젝트에 유용하도록 기계 학습 파이프라인이 추가로 개선되었습니다(새 손실 계층).

## <a name="june-28-2018"></a>2018년 6월 28일

- 백 엔드 기능을 & 버그 수정
- 다중 클래스 분류를 사용 하도록 설정 합니다. 다중 클래스 모드의 예측에서 확률은 1로 합산 됩니다 (모든 이미지는 지정 된 태그 사이에서 분류 됨).

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

- 유료 미리 보기를 입력 하 고 Azure Portal에 등록. 이제 F0(무료) 또는 S0(표준) 계층으로 Azure 리소스에 프로젝트를 연결할 수 있습니다. 최대 태그 100개 이미지 25,000개까지 허용하는 S0 계층 프로젝트가 도입되었습니다.
- 백 엔드가 기계 학습 파이프 라인/정규화 매개 변수로 변경됩니다. 따라서 확률 임계값을 조정할 때 고객이 정밀도-회수 상충 관계를 더 잘 제어할 수 있습니다. 이러한 변화의 일환으로, CustomVision.ai 포털의 기본 확률 임계값이 50%로 설정되었습니다.

## <a name="december-19-2017"></a>2017년 12월 19일

- 이전에 릴리스된 iOS로 내보내기 (CoreML) 외에도 추가 된 Android (TensorFlow)로 내보내기 이렇게 하면 학습 된 compact 모델을 내보낼 때 응용 프로그램에서 오프 라인으로 실행할 수 있습니다.
- 소매 및 랜드마크 “컴팩트” 도메인이 추가되어 이러한 도메인에 대한 모델 내보내기가 가능합니다.
- 버전 [1.2 교육 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 및 [1.1 예측 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)가 릴리스되었습니다. 업데이트된 API는 모델 내보내기, "예측"에 이미지를 저장하지 않는 새로운 예측 작업을 지원하며 교육 API에 일괄 처리 작업을 도입했습니다.
- 반복 학습에 사용된 도메인을 확인하는 기능을 비롯한 UX가 조정되었습니다.
- [C# SDK 및 샘플](https://github.com/Microsoft/Cognitive-CustomVision-Windows)이 업데이트되었습니다.