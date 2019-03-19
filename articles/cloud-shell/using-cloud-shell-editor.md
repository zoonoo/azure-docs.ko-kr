---
title: Azure Cloud Shell 편집기 사용 | Microsoft Docs
description: Azure Cloud Shell 편집기 사용 방법 개요.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243162"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Azure Cloud Shell 편집기 사용

Azure Cloud Shell에는 오픈 소스 [Monaco 편집기](https://github.com/Microsoft/monaco-editor)에서 빌드된 통합 파일 편집기가 포함됩니다. Cloud Shell 편집기는 언어 강조 표시, 명령 팔레트 및 파일 탐색기 등의 기능을 지원합니다.

![Cloud Shell 편집기](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>편집기 열기

간단한 파일을 생성하고 편집하는 경우 Cloud Shell 터미널에서 `code .`를 실행하여 편집기를 시작합니다. 이 작업은 터미널에서 설정된 활성 작업 디렉터리를 사용하여 편집기를 엽니다.

빠른 편집을 위해 파일을 직접 열려면 `code <filename>`을 실행하여 파일 탐색기 없이 편집기를 엽니다.

UI 단추를 통해 편집기를 열려면 도구 모음에서 `{}` 편집기 아이콘을 클릭합니다. 그러면 편집기를 열고 `/home/<user>` 디렉터리에 대한 파일 탐색기를 기본값으로 설정합니다.

## <a name="closing-the-editor"></a>편집기 닫기

편집기를 닫으려면 편집기의 오른쪽 위에서 `...` 작업 패널을 열고 `Close editor`를 선택합니다.

![편집기 닫기](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>명령 팔레트

명령 팔레트를 시작하려면 포커스를 편집기에 설정할 때 `F1` 키를 사용합니다. 명령 팔레트를 여는 작업은 작업 패널을 통해 수행될 수도 있습니다.

![Cmd 팔레트](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Monaco 편집기에 참여

Cloud Shell 편집기에서 언어 강조 표시 지원은 Monarch 구문 정의의 [Monaco 편집기](https://github.com/Microsoft/monaco-editor)를 사용하는 업스트림 기능을 통해 지원됩니다. 참여하는 방법을 알아보려면 [Monaco 참여자 가이드](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Cloud Shell에서 Bash에 대한 빠른 시작 사용해 보기](quickstart.md)
[통합 Cloud Shell 도구의 전체 목록 보기](features.md)