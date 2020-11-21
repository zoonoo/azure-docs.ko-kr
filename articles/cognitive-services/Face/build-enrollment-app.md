---
title: 반응으로 Android 용 등록 앱 빌드
titleSuffix: Azure Cognitive Services
description: 개발 환경을 설정 하 고 얼굴 등록 앱을 배포 하 여 고객의 동의를 받는 방법에 대해 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: bd2032d565f5bd1fb430449be8b8c08e222f531d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025785"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>반응으로 Android 용 등록 앱 빌드

이 가이드에서는 샘플 얼굴 등록 응용 프로그램을 시작 하는 방법을 보여 줍니다. 앱은 사용자를 얼굴 인식 서비스에 등록 하 고 높은 정확도 얼굴 데이터를 얻기 위한 의미 있는 동의를 얻기 위한 모범 사례를 보여 줍니다. 통합 시스템은 이와 같은 등록 앱을 사용 하 여 얼굴 데이터를 기반으로 touchless access control, identity 확인, 참석 추적, 개인 설정 키오스크 또는 id 확인을 제공할 수 있습니다.

응용 프로그램을 시작 하면 사용자에 게 자세한 동의 화면이 표시 됩니다. 사용자가 동의 하면 앱은 사용자 이름 및 암호를 묻는 메시지를 표시 한 다음 장치의 카메라를 사용 하 여 고화질 얼굴 이미지를 캡처합니다.

샘플 등록 앱은 JavaScript 및 반응 네이티브 프레임 워크를 사용 하 여 작성 됩니다. 현재 Android 장치에 배포할 수 있습니다. 나중에 더 많은 배포 옵션을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건 

* Azure 구독- [무료로 무료로 만드세요](https://azure.microsoft.com/free/cognitive-services/).  
* Azure 구독이 있으면 Azure Portal에서 [얼굴 리소스를 만들어](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) 키와 끝점을 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.  
  * 응용 프로그램을 Face API에 연결 하려면 만든 리소스의 키와 끝점이 필요 합니다.  
  * 로컬 개발 및 테스트를 위해 API 키와 끝점을 구성 파일에 붙여 넣을 수 있습니다. 최종 배포의 경우 API 키를 안전한 위치에 저장 하 고 코드에는 저장 하지 않습니다.  

> [!IMPORTANT]
> 이러한 구독 키는 Cognitive Service API에 액세스하는 데 사용됩니다. 키를 공유하지 마세요. 예를 들어 Azure Key Vault를 사용 하 여 안전 하 게 저장 합니다. 또한 이러한 키는 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 수행하는 데는 하나의 키만 필요합니다. 첫 번째 키를 다시 생성하는 경우 두 번째 키를 사용하여 서비스에 계속 액세스할 수 있습니다.

## <a name="set-up-the-development-environment"></a>개발 환경 설정

1. [샘플 등록 앱](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)에 대 한 git 리포지토리를 복제 합니다.
1. 개발 환경을 설정 하려면 네이티브 설명서에 반응 하 여 <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> 네이티브 설명서를 참조 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 하세요. 개발 OS로 **기본 CLI 빠른** 시작을 선택 하 고 대상 OS로 **Android** 를 선택 합니다. 종속성 및 **Android 개발 환경** **설치** 섹션을 완료 합니다.
1. [Visual Studio Code](https://code.visualstudio.com/)와 같은 원하는 텍스트 편집기에서 env.js파일을 열고 끝점과 키를 추가 합니다. 리소스의 **개요** 탭에 있는 Azure Portal에서 끝점 및 키를 가져올 수 있습니다. 이 단계는 로컬 테스트 목적 으로만 사용 되며 &mdash; Face API 키를 원격 리포지토리에 체크 인 하지 않습니다.
1. Android Studio 또는 사용자 고유의 Android 장치에서 Android 가상 장치 에뮬레이터를 사용 하 여 앱을 실행 합니다. 물리적 장치에서 앱을 테스트 하기 위해 네이티브 설명서에 반응 하는 관련 작업을 수행 합니다 <a href="https://reactnative.dev/docs/running-on-device"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .  


## <a name="create-an-enrollment-experience"></a>등록 환경 만들기  

이제 샘플 등록 앱을 설정 했으므로 자신의 등록 환경 요구 사항에 맞게 사용자 지정할 수 있습니다.

예를 들어 동의 페이지에서 상황에 맞는 정보를 추가 하려고 할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![앱 승인 페이지](./media/enrollment-app/1-consent-1.jpg)

이 서비스는 이미지의 품질이 고객을 등록 하거나 얼굴을 인식 하는 데 충분 한지를 선택 하는 데 도움이 되는 이미지 품질 검사를 제공 합니다. 이 앱은 장치의 카메라에서 프레임에 액세스 하 고, 최고 품질의 프레임을 선택 하 고, 검색 된 얼굴을 Face API 서비스에 등록 하는 방법을 보여 줍니다. 

많은 얼굴 인식 문제는 품질이 낮은 참조 이미지에 의해 발생 합니다. 모델 성능을 저하 시킬 수 있는 몇 가지 요소는 다음과 같습니다.
* 얼굴 크기 (카메라에서 멀리 떨어진 얼굴)
* 얼굴 방향 (카메라에서 얼굴 이동 또는 기울어짐)
* 이미지가 잘못 노출 되었거나 너무 큰 조명 조건 (낮은 빛 또는 backlighting)이 있습니다.
* 폐색 (부분적으로 숨겨짐 또는 고가는 얼굴) (예: 모자 또는 테두리 거 없는 액세서리 포함)
* 흐림 효과 (예: 사진을 찍은 때 빠른 얼굴 이동) 

> [!div class="mx-imgBorder"]
> ![앱 이미지 캡처 명령 페이지](./media/enrollment-app/4-instruction.jpg)

앱은 사용자의 등록을 삭제 하는 기능 및 다시 등록 하는 옵션을 제공 합니다.

> [!div class="mx-imgBorder"]
> ![프로필 관리 페이지](./media/enrollment-app/10-manage-2.jpg)

전체 등록 환경을 포함 하도록 앱 기능을 확장 하려면 구현 및 모범 사례에 대 한 추가 기능에 대 한 [개요](enrollment-overview.md) 를 참조 하세요.

## <a name="deploy-the-enrollment-app"></a>등록 앱 배포

### <a name="android"></a>Android

먼저 앱이 프로덕션 배포에 대해 준비 되었는지 확인 합니다. 앱 코드에서 키 또는 비밀을 제거 하 고 [보안 모범 사례](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp)를 따르고 있는지 확인 합니다.

프로덕션을 위해 앱을 릴리스할 준비가 되 면 Android 앱에 대 한 패키지 파일 형식인 릴리스가 준비 된 APK 파일을 생성 합니다. 이 APK 파일은 개인 키로 서명 해야 합니다. 이 릴리스 빌드를 사용 하 여 장치에 직접 앱을 배포할 수 있습니다. 

릴리스 준비에 대 한 릴리스 준비 설명서를 따라 <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 개인 키를 생성 하 고, 응용 프로그램에 서명 하 고, 릴리스 apk를 생성 하는 방법을 알아봅니다.  

서명 된 APK를 만들었으면 앱 게시 <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 설명서를 참조 하 여 앱을 릴리스 하는 방법에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계  

이 가이드에서는 개발 환경을 설정 하 고 샘플 등록 앱을 시작 하는 방법을 배웠습니다. 네이티브에 반응 하는 데 익숙하지 않은 경우 [시작 문서](https://reactnative.dev/docs/getting-started) 를 참조 하 여 자세한 배경 정보를 확인할 수 있습니다. 또한 [Face API](Overview.md)를 익히는 데 도움이 될 수도 있습니다. 개발을 시작 하기 전에 등록 앱 설명서의 다른 섹션을 읽어 보세요.
