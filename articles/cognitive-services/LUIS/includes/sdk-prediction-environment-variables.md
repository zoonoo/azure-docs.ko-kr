---
title: 포함 파일
description: 포함 파일
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772393"
---
### <a name="create-an-environment-variable"></a>환경 변수 만들기

런타임 키와 런타임 엔드포인트를 사용하여 인증 및 액세스를 위한 환경 변수를 만듭니다.

* `LUIS_RUNTIME_KEY` - 요청을 인증하기 위한 런타임 리소스 키입니다.
* `LUIS_RUNTIME_ENDPOINT` - 키와 연결된 런타임 엔드포인트입니다.
* `LUIS_APP_ID` - 공용 LUIS IoT 앱 ID는 `df67dcdb-c37d-46af-88e1-8b97951ca1c2`입니다.
* `LUIS_APP_SLOT_NAME` - `production` 또는 `staging`

이 빠른 시작을 사용하여 자신의 앱에 액세스하려는 경우 추가 단계를 수행해야 합니다.
* 앱 만들기 및 앱 ID 가져오기
* LUIS 포털에서 LUIS 앱에 런타임 키 할당
* 앱에 액세스할 수 있는 브라우저를 사용하여 URL을 테스트합니다.

운영 체제에 대한 지침을 사용합니다.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

환경 변수를 추가한 후 콘솔 창을 다시 시작합니다.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

환경 변수를 추가한 후 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

`.bash_profile`을 편집하고, 환경 변수를 추가합니다.

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

환경 변수를 추가한 후 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.

***
