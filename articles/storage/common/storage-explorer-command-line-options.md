---
title: Azure Storage 탐색기 명령줄 옵션 | Microsoft Docs
description: Azure Storage 탐색기 시작 명령줄 옵션 설명서
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745409"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure Storage 탐색기 명령줄 옵션

Microsoft Azure Storage 탐색기에는 응용 프로그램을 시작할 때 추가 될 수 있는 명령줄 옵션 집합이 있습니다. 대부분의 명령줄 옵션은 디버깅 또는 문제 해결을 목적으로 합니다.

## <a name="command-line-options"></a>명령줄 옵션
옵션  | 설명
:------- | :-----------
`--debug`/`--prod`  | 디버그 또는 프로덕션 모드에서 응용 프로그램을 시작 합니다. 디버그 모드에서 로컬 첨부 파일 데이터는 응용 프로그램의 로컬 저장소에 저장 되며 암호화 되지 않습니다. 숨겨진 속성은 선택한 리소스 노드에 대 한 속성 패널에 표시 됩니다. 로그의 자세한 정도 수준은 Storage 탐색기의 내부 설정 논리를 표시 하는 디버그 메시지를 인쇄 하도록 설정 됩니다. 기본값은 `--prod`입니다.
`--lang`  | 지정 된 언어로 응용 프로그램을 시작 합니다. 예: `--lang="zh-Hans"`
`--disable-gpu` | GPU 가속 없이 응용 프로그램을 시작 합니다.
`--auto-open-dev-tools` | 브라우저 창이 표시 되는 즉시 응용 프로그램이 개발자 도구 창을 열도록 합니다. 이 옵션은 브라우저 창의 시작 코드 줄에서 중단점을 적중 하려는 경우에 유용 합니다.
`--verbosity` | Storage 탐색기 로깅의 자세한 정도 수준을 설정 합니다. 지원 되는 자세한 정보 표시 수준 `debug` 에는,,,, 및가 `verbose` `info` `warn` `error` `silent` 있습니다. 예: `--verbosity=verbose` 프로덕션 모드에서 실행 하는 경우 기본 세부 정보 표시 수준은 `info` 입니다. 디버그 모드에서 실행 하는 경우 로그의 자세한 정도는 항상 `debug` 입니다.
`--log-dir` | 로그 파일을 저장할 디렉터리를 설정 합니다. 예: `--log-dir=path_to_a_directory`

사용자 지정 명령줄 옵션을 사용 하 여 Storage 탐색기를 시작 하는 예제

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> 이러한 명령줄 옵션은 새 Storage 탐색기 버전에서 변경 될 수 있습니다.

## <a name="when-to-use-command-line-options"></a>명령줄 옵션을 사용 하는 경우

일부 명령줄 옵션을 사용 하 여 Storage 탐색기를 사용자 지정할 수 있습니다. 와 같은 해당 사용자 설정이 있는 옵션입니다 `--lang` . 명령줄 옵션을 사용 하는 대신 사용자 설정을 사용 하는 것이 좋습니다. 

다른 명령줄 옵션은 디버깅 및 문제 해결에 유용할 수 있습니다. Storage 탐색기에서 문제가 발생 하는 경우 디버그 모드에서 문제를 재현 하면 더 자세한 정보를 조사 하는 데 도움이 될 수 있습니다.