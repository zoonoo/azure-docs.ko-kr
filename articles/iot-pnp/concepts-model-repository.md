---
title: 장치 모델 리포지토리의 개념 이해 | Microsoft Docs
description: 솔루션 개발자 또는 IT 전문가는 장치 모델 리포지토리의 기본 개념에 대해 알아봅니다.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b567efe2541bb33c905def73bb78398799b4ed69
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920545"
---
# <a name="device-model-repository"></a>장치 모델 리포지토리

장치 모델 리포지토리 (DMR)를 사용 하 여 장치 빌더가 IoT 플러그 앤 플레이 장치 모델을 관리 하 고 공유할 수 있습니다. 장치 모델은 [DTDL (디지털 Twins 모델링 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)을 사용 하 여 정의 된 JSON LD 문서입니다.

DMR는 DTDL (장치 쌍 모델 식별자)를 기반으로 하는 폴더 구조에 DTDL 인터페이스를 저장 하는 패턴을 정의 합니다. DTMI를 상대 경로로 변환 하 여 DMR에서 인터페이스를 찾을 수 있습니다. 예를 들어 `dtmi:com:example:Thermostat;1` dtmi는를로 변환 합니다 `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>공용 장치 모델 리포지토리

Microsoft는 다음과 같은 특징을 가진 공용 DMR을 호스팅합니다.

- 큐 레이트 모델 Microsoft는 GitHub 끌어오기 요청 (PR) 유효성 검사 워크플로를 사용 하 여 사용 가능한 모든 인터페이스를 검토 하 고 승인 합니다.
- 불변성.  게시 된 후에는 인터페이스를 업데이트할 수 없습니다.
- 하이퍼-규모. Microsoft는 장치 모델을 게시 하 고 사용할 수 있는 안전 하 고 확장 가능한 끝점을 만드는 데 필요한 인프라를 제공 합니다.

## <a name="custom-device-model-repository"></a>사용자 지정 장치 모델 리포지토리

동일한 DMR 패턴을 사용 하 여 로컬 파일 시스템 또는 사용자 지정 HTTP 웹 서버와 같은 모든 저장소 미디어에서 사용자 지정 DMR를 만듭니다. DMR에 액세스 하는 데 사용 되는 기본 URL을 변경 하 여 공용 DMR와 동일한 방식으로 사용자 지정 DMR에서 장치 모델을 검색할 수 있습니다.

> [!NOTE]
> Microsoft는 공용 DMR에서 장치 모델의 유효성을 검사 하는 도구를 제공 합니다. 사용자 지정 리포지토리에서 이러한 도구를 다시 사용할 수 있습니다.

## <a name="public-models"></a>공용 모델

모델 리포지토리에 저장 된 공용 장치 모델은 모든 사용자가 응용 프로그램에서 사용 하 고 통합 하는 데 사용할 수 있습니다. 공용 장치 모델을 사용 하면 장치 빌더 및 솔루션 개발자가 IoT 플러그 앤 플레이 장치 모델을 공유 하 고 다시 사용할 수 있습니다.

모델 리포지토리에 모델을 게시 하 여 공용으로 설정 하는 방법에 대 한 지침은 [모델 게시](#publish-a-model) 섹션을 참조 하세요.

사용자는 공식 [GitHub 리포지토리에서](https://github.com/Azure/iot-plugandplay-models)공용 인터페이스를 찾아보고 검색 하 고 볼 수 있습니다.

`dtmi`공용 끝점에서 폴더의 모든 인터페이스를 사용할 수도 있습니다.[https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>모델 확인

이러한 인터페이스에 프로그래밍 방식으로 액세스 하려면 DTMI를 공용 끝점을 쿼리 하는 데 사용할 수 있는 상대 경로로 변환 해야 합니다.

DTMI를 절대 경로로 변환 하려면 `DtmiToPath` 함수와 함께 함수를 사용 합니다 `IsValidDtmi` .

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

결과 경로와 리포지토리의 기본 URL을 사용 하 여 인터페이스를 가져올 수 있습니다.

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>모델 게시

> [!Important]
> 공용 DMR에 모델을 제출할 수 있으려면 GitHub 계정이 있어야 합니다.

1. 공용 GitHub 리포지토리를 포크 [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) 합니다.
1. 분기 리포지토리를 복제 합니다. 필요에 따라 분기에서 변경 내용을 격리 되도록 새 분기를 만듭니다 `main` .
1. `dtmi`폴더/파일 이름 규칙을 사용 하 여 폴더에 새 인터페이스를 추가 합니다. 자세히 알아보려면 [ `dtmi/` 폴더로 모델 가져오기](#import-a-model-to-the-dtmi-folder)를 참조 하세요.
1. 도구를 사용 하 여 로컬로 모델의 유효성을 검사 합니다 `dmr-client` . 자세히 알아보려면 [모델 유효성 검사](#validate-models)를 참조 하세요.
1. 로컬에서 변경 내용을 커밋하고 포크로 푸시합니다.
1. 포크에서 분기를 대상으로 하는 끌어오기 요청을 만듭니다 `main` . [문제 만들기 또는 끌어오기 요청](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) 문서를 참조 하세요.
1. [끌어오기 요청 요구 사항을](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)검토 합니다.

끌어오기 요청은 제출 된 인터페이스의 유효성을 검사 하는 GitHub 작업 집합을 트리거하고 끌어오기 요청이 모든 요구 사항을 충족 하는지 확인 합니다.

Microsoft는 영업일 3 일 이내에 모든 검사를 수행 하 여 끌어오기 요청에 응답 합니다.

### <a name="dmr-client-tools"></a>`dmr-client` 도구

PR 검사 중 모델의 유효성을 검사 하는 데 사용 되는 도구를 사용 하 여 DTDL 인터페이스를 로컬로 추가 하 고 유효성을 검사할 수도 있습니다.

> [!NOTE]
> 이 도구를 사용 하려면 [.NET SDK](https://dotnet.microsoft.com/download) 버전 3.1 이상이 필요 합니다.

### <a name="install-dmr-client"></a>`dmr-client` 설치

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>모델을 `dtmi/` 폴더로 가져오기

모델이 json 파일에 이미 저장 되어 있는 경우 `dmr-client import` 명령을 사용 하 여 `dtmi/` 올바른 파일 이름으로 폴더에 추가할 수 있습니다.

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> 인수를 사용 `--local-repo` 하 여 로컬 리포지토리 루트 폴더를 지정할 수 있습니다.

### <a name="validate-models"></a>모델 유효성 검사

다음 명령을 사용 하 여 모델의 유효성을 검사할 수 있습니다 `dmr-client validate` .

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> 유효성 검사에서는 최신 DTDL 파서 버전을 사용 하 여 모든 인터페이스가 DTDL 언어 사양과 호환 되는지 확인 합니다.

외부 종속성의 유효성을 검사 하려면 로컬 리포지토리에 있어야 합니다. 모델의 유효성을 검사 하려면 다음 옵션을 사용 하 여 `--repo` `local` 종속성을 확인할 또는 폴더를 지정 합니다 `remote` .

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>엄격한 유효성 검사

DMR에는 추가 [요구 사항이](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)포함 되어 있습니다 `stict` . 플래그를 사용 하 여 모델의 유효성을 검사 합니다.

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

콘솔 출력에서 오류 메시지를 확인 합니다.

### <a name="export-models"></a>모델 내보내기

JSON 배열을 사용 하 여 지정 된 리포지토리 (로컬 또는 원격)에서 단일 파일로 모델을 내보낼 수 있습니다.

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>다음 단계

제안 된 다음 단계는 [IoT 플러그 앤 플레이 아키텍처](concepts-architecture.md)를 검토 하는 것입니다.
