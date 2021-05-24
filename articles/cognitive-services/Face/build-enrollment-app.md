---
title: React로 Android용 등록 앱 빌드
titleSuffix: Azure Cognitive Services
description: 개발 환경을 설정하고 Face 등록 앱을 배포하여 고객의 동의를 받는 방법에 대해 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 218579176b807bbdae85646f27eaa7f301d4b9a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102428272"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>React로 Android용 등록 앱 빌드

이 가이드에서는 샘플 Face 등록 애플리케이션을 시작하는 방법을 설명합니다. 앱은 사용자를 얼굴 인식 서비스에 등록하고 높은 정확도의 얼굴 데이터를 얻기 위해 의미 있는 동의를 얻는 것과 관련된 모범 사례를 보여줍니다. 통합 시스템은 이와 같은 등록 앱을 사용하여 얼굴 데이터를 기반으로 비접촉 액세스 제어, ID 확인, 참석 추적 또는 개인 설정 키오스크를 제공할 수 있습니다.

애플리케이션을 시작하면 사용자에게 자세한 동의 화면이 표시됩니다. 사용자가 동의하면 앱은 사용자 이름 및 암호를 묻는 메시지를 표시한 다음, 디바이스의 카메라를 사용하여 고화질 얼굴 이미지를 캡처합니다.

샘플 등록 앱은 JavaScript 및 React Native 프레임 워크를 사용하여 작성되었습니다. 현재 Android 디바이스에 배포할 수 있습니다. 향후에는 더 많은 배포 옵션을 사용할 수 있을 것입니다.

## <a name="prerequisites"></a>사전 요구 사항 

* Azure 구독 – [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)  
* Azure 구독을 보유한 후에는 Azure Portal에서 [Face 리소스를 만들어](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.  
  * 애플리케이션을 Face API에 연결하려면 생성한 리소스의 키와 엔드포인트가 필요합니다.  
  * 로컬 개발 및 테스트를 위해 API 키와 엔드포인트를 구성 파일에 붙여넣을 수 있습니다. 최종 배포의 경우 API 키를 안전한 위치에 저장하고 코드에는 저장하지 않습니다.  

> [!IMPORTANT]
> 이러한 구독 키는 Cognitive Service API에 액세스하는 데 사용됩니다. 키를 공유하지 마세요. 예를 들어 Azure Key Vault를 사용하여 안전하게 저장합니다. 또한 이러한 키는 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 수행하는 데는 하나의 키만 필요합니다. 첫 번째 키를 다시 생성하는 경우 두 번째 키를 사용하여 서비스에 계속 액세스할 수 있습니다.

## <a name="set-up-the-development-environment"></a>개발 환경 설정

1. [샘플 등록 앱](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)에 대한 git 리포지토리를 복제합니다.
1. 개발 환경을 설정하려면 <a href="https://reactnative.dev/docs/environment-setup"  title="React Native 설명서"  target="_blank">React Native 설명서</a>를 참조하세요. 개발 OS로 **React Native CLI 빠른 시작** 을 선택하고 대상 OS로 **Android** 를 선택합니다. **종속성 설치** 및 **Android 개발 환경** 섹션을 완료합니다.
1. [Visual Studio Code](https://code.visualstudio.com/)와 같이 선호하는 텍스트 편집기에서 env.json 파일을 열고 엔드포인트와 키를 추가합니다. 리소스의 **개요** 탭에 있는 Azure Portal에서 엔드포인트 및 키를 가져올 수 있습니다. 이 단계는 로컬 테스트 목적으로만 사용됩니다.&mdash;Face API 키를 원격 리포지토리에 체크 인하지 마세요.
1. Android Studio의 Android 가상 디바이스 에뮬레이터나 사용자의 Android 디바이스를 사용하여 앱을 실행합니다. 물리적 디바이스에서 앱을 테스트하려면 관련 <a href="https://reactnative.dev/docs/running-on-device"  title="React Native 설명서"  target="_blank">React Native 설명서</a>를 따르세요.  


## <a name="create-an-enrollment-experience"></a>등록 환경 만들기  

이제 샘플 등록 앱을 설정했으므로 자신의 등록 환경 요구 사항에 맞게 조정할 수 있습니다.

예를 들어 동의 페이지에서 상황에 맞는 정보를 추가하려고 할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![앱 동의 페이지](./media/enrollment-app/1-consent-1.jpg)

이 서비스는 이미지의 품질이 고객을 등록하거나 얼굴 인식을 시도하는데 충분한지 확인하는 데 도움이 되는 이미지 품질 검사를 제공합니다. 이 앱은 디바이스의 카메라에서 프레임에 액세스하고, 최고 품질의 프레임을 선택하고, 검색된 얼굴을 Face API 서비스에 등록하는 방법을 보여줍니다. 

대부분의 얼굴 인식 문제는 품질이 낮은 참조 이미지 때문에 발생합니다. 모델 성능을 저하시킬 수 있는 몇 가지 요소는 다음과 같습니다.
* 얼굴 크기(카메라에서 멀리 떨어진 얼굴)
* 얼굴 방향(카메라에서 얼굴이 돌아갔거나 기울어짐)
* 이미지의 노출이 부족하거나 노이즈가 너무 많은 조명이 부족한 조건(적은 빛 또는 역광)
* 모자나 테가 두꺼운 안경을 포함한 얼굴 가림(부분적으로 숨겨지거나 가려진 얼굴)
* 흐린 얼굴(예: 사진을 찍은 때 빠르게 움직인 얼굴) 

> [!div class="mx-imgBorder"]
> ![앱 이미지 캡처 명령 페이지](./media/enrollment-app/4-instruction.jpg)

앱은 사용자의 등록을 삭제하는 기능과 다시 등록하는 옵션을 제공합니다.

> [!div class="mx-imgBorder"]
> ![프로필 관리 페이지](./media/enrollment-app/10-manage-2.jpg)

전체 등록 환경을 포함하도록 앱 기능을 확장하려면 구현할 수 있는 추가 기능과 모범 사례에 대한 [개요](enrollment-overview.md)를 참조하세요.

## <a name="deploy-the-enrollment-app"></a>등록 앱 배포

### <a name="android"></a>Android

먼저 앱이 프로덕션을 배포할 준비가 되었는지 확인합니다. 앱 코드에서 키 또는 비밀을 제거하고 [보안 모범 사례](../cognitive-services-security.md?tabs=command-line%2ccsharp)를 따르고 있는지 확인합니다.

프로덕션용 앱을 릴리스할 준비가 되면 Android 앱용 패키지 파일 형식인 릴리스 가능 APK 파일을 생성합니다. 이 APK 파일은 프라이빗 키로 서명해야 합니다. 이 릴리스 빌드를 사용하여 디바이스에 직접 앱을 배포할 수 있습니다. 

<a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="릴리스 준비"  target="_blank">릴리스 준비</a> 설명서에 따라 프라이빗 키를 생성하고, 애플리케이션에 서명하고, 릴리스 APK를 생성하는 방법을 알아보세요.  

서명된 APK를 만들었으면 <a href="https://developer.android.com/studio/publish"  title="앱 게시"  target="_blank">앱 게시</a> 설명서를 참조하여 앱을 릴리스하는 방법에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계  

이 가이드에서는 개발 환경을 설정하고 샘플 등록 앱을 시작하는 방법을 배웠습니다. React Native에 익숙하지 않은 경우 [시작하기 Docs](https://reactnative.dev/docs/getting-started)를 참조하여 자세한 배경 정보를 확인할 수 있습니다. 또한 [Face API](Overview.md)를 익히는 데도 도움이 될 것입니다. 개발을 시작하기 전에 등록 앱 설명서의 다른 섹션을 읽어 보세요.