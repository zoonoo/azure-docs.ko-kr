---
title: 설정을 관리하는 방법 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator에서 설정을 관리하고, 작업 영역을 만들고, 작업 영역을 공유하며 구독 키를 관리하는 방법입니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895784"
---
# <a name="how-to-manage-settings"></a>설정을 관리하는 방법

Custom Translator 설정 페이지에서 작업 영역을 공유하고, Translator 구독 키를 수정하고, 작업 영역을 삭제할 수 있습니다.

설정 페이지에 액세스하려면

1. [Custom Translator](https://portal.customtranslator.azure.ai/) 포털에 로그인합니다.
2. Custom Translator 포털에서 사이드바에서 기어 아이콘을 클릭합니다.

    ![링크 설정](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Translator 구독 연결

모델을 학습 또는 배포하려면 작업 영역과 연결된 Translator 구독 키가 필요합니다.

구독이 없으면 다음 단계를 수행합니다.

1. Translator 리소스를 만들려면 구독합니다. [Translator를 등록하는 방법](../translator-how-to-signup.md)에 따라 구독하고 Translator 키를 가져옵니다.
2. Translator 구독에 대한 키를 적어둡니다. Key1 또는 Key2가 허용됩니다.
3. Custom Translator 포털로 다시 이동합니다.

## <a name="create-a-new-workspace"></a>새 작업 영역 만들기

1. "Custom Translator" 사이드바에서 "+ 작업 영역 만들기" 단추를 클릭합니다.

    ![새 작업 영역 만들기](media/how-to/create-new-workspace.png)

2. 대화 상자에서 새 작업 영역의 이름을 입력합니다.
3. “다음”을 클릭합니다.
4. 구독 유형을 선택합니다.
5. 구독 지역을 선택합니다. 이 지역은 Translator 리소스 키를 만들 때 선택한 지역과 일치해야 합니다.
6. Translator 구독을 위한 키를 입력하고 "저장" 단추를 클릭합니다.

    ![새 작업 영역 만들기 대화 상자](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>Custom Translator는 [사용하도록 설정된 VNET](../../../api-management/api-management-using-with-vnet.md) 내에서 만들어진 Translator Text API 리소스(Azure 구독 키라고도 함)에 대한 작업 영역 만들기를 지원하지 않습니다.

### <a name="modify-existing-key"></a>기존 키 수정

1. 작업 영역의 “설정” 페이지로 이동합니다.
2. 키 변경을 클릭합니다.

    ![구독 키를 추가하는 방법](media/how-to/how-to-add-subscription-key.png)

3. 대화 상자에서 Translator 구독에 대한 키를 입력한 다음 "저장" 단추를 클릭합니다.

    ![구독 키를 추가하는 방법 대화 상자](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>작업 영역 관리

작업 영역이란 사용자 지정 번역 시스템을 구성하고 빌드하기 위한 작업 구축에 대 한 작업 공간입니다. 적업 영역은 여러 프로젝트, 모델 및 문서를 포함할 수 있습니다.

작업의 다른 부분을 다른 사람들과 공유해야 하는 경우 여러 작업 영역을 만드는 것이 유용할 수 있습니다.

## <a name="share-your-workspace"></a>작업 영역 공유

Custom Translator에서는 작업의 다른 부분을 다른 사람들과 공유해야 하는 경우 작업 영역을 다른 사용자와 공유할 수 있습니다.

1. 작업 영역 “설정” 페이지로 이동합니다.
2. "공유 설정" 섹션에서 "사람 추가" 단추를 클릭합니다.

    ![작업 영역 공유](media/how-to/share-workspace.png)

3. 대화 상자에서 이 작업 영역을 공유할 이메일 주소의 목록을 쉼표로 구분하여 입력합니다. Custom Translator에 로그인하는 데 사용하는 이메일 주소를 공유해야 합니다. 그런 다음 적절한 수준의 공유 권한을 선택하고 "저장" 단추를 클릭합니다.

    ![작업 영역 공유 대화 상자](media/how-to/share-workspace-dialog.png)

4. 작업 영역에 여전히 기본 이름 “내 작업 영역”이 있는 경우 작업 영역을 공유하기 전에 변경해야 합니다.
5. “저장”을 클릭합니다.

## <a name="sharing-permissions"></a>공유 권한

1. **읽기 권한자:** 작업 영역의 읽기 권한자는 작업 영역에서 모든 정보를 볼 수 있게 됩니다.

2. **편집자:** 작업 영역의 편집자는 문서를 추가하고, 모델을 학습하고, 문서 및 프로젝트를 삭제할 수 있습니다. 구독 키를 추가할 수는 있지만 작업 영역을 공유하는 사람을 수정하거나 작업 영역을 삭제하거나 작업 영역 이름을 변경할 수는 없습니다.

3. **소유자:** 소유자는 작업 영역에 대한 모든 권한을 갖습니다.

## <a name="change-sharing-permission"></a>공유 권한 변경

작업 영역이 공유되면 “공유 설정” 섹션에 이 작업 영역을 공유하는 모든 이메일 주소가 표시됩니다. 작업 영역에 소유자 액세스 권한이 있는 사용자는 각 이메일 주소에 대한 기존 공유 권한을 변경할 수 있습니다.

1. 각 이메일에 대한 “공유 설정” 섹션에 있는 드롭다운 메뉴에 현재 권한 수준이 표시됩니다.

2. 드롭다운 메뉴를 클릭하고 해당 이메일 주소에 할당하려는 새로운 권한 수준을 선택합니다.

    ![공유 권한 설정](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>작업 영역 고정

작업 영역을 처음 만들면 기본적으로 고정되어 있습니다. 로그인할 때마다 사이트 로드 시 고정된 작업 영역이 표시됩니다. 여러 작업 영역을 만들고 로그인할 때 만든 작업 영역 중 하나를 고정하여 기본값으로 설정할 수 있습니다.

1. 사이드바에서 고정하려는 작업 영역의 이름을 클릭합니다.
2. 작업 영역의 “설정” 페이지로 이동합니다.
3. 고정 아이콘을 클릭합니다.

    ![작업 영역 고정](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>다음 단계

- [작업 영역 및 프로젝트를 만드는 방법](workspace-and-project.md)에 대해 알아보기