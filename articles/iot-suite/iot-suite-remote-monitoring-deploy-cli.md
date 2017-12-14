---
title: "Java 원격 모니터링 솔루션 배포 - Azure | Microsoft Docs"
description: "이 자습서는 CLI를 사용하여 미리 구성된 원격 모니터링 솔루션 Java 마이크로 서비스를 프로비전하는 방법을 보여 줍니다."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: ea3764299d07f548abbc2857a3adbfb4dc50dec8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>CLI를 사용하여 미리 구성된 원격 모니터링 솔루션 배포

이 자습서는 미리 구성된 원격 모니터링 솔루션을 프로비전하는 방법을 보여 줍니다. CLI를 사용하여 솔루션을 배포합니다. azureiotsuite.com에서 웹 기반 UI를 사용하여 솔루션을 배포할 수도 있습니다. 이 옵션에 대해 알아보려면 [미리 구성된 원격 모니터링 솔루션 배포](iot-suite-remote-monitoring-deploy.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

미리 구성된 원격 모니터링 솔루션을 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

CLI를 실행하려면 [Node.js](https://nodejs.org/)를 로컬 컴퓨터에 설치해야 합니다.

## <a name="install-the-cli"></a>CLI 설치

CLI를 설치하려면 명령줄 환경에서 다음 명령을 실행합니다.

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>CLI에 로그인

미리 구성된 솔루션을 배포하기 전에 다음과 같이 CLI를 사용하여 Azure 구독에 로그인해야 합니다.

```cmd/sh
pcs login
```

화면의 지시에 따라 로그인 프로세스를 완료합니다.

## <a name="deployment-options"></a>배포 옵션

미리 구성된 솔루션을 배포하는 경우 배포 프로세스를 구성하는 여러 옵션이 있습니다.

| 옵션 | 값 | 설명 |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard` | _기본_ 배포는 테스트 및 데모를 위한 것이며 단일 가상 컴퓨터에 모든 마이크로 서비스를 배포합니다. _표준_ 배포는 프로덕션을 위한 것이며 여러 가상 컴퓨터에 마이크로 서비스를 배포합니다. |
| 런타임 | `dotnet`, `java` | 마이크로 서비스의 언어 구현을 선택합니다. |

## <a name="deploy-the-preconfigured-solution"></a>미리 구성된 솔루션 배포

### <a name="example-deploy-net-version"></a>예제: .NET 버전 배포

다음 예제는 미리 구성된 원격 모니터링 솔루션의 기본, .NET 버전을 배포하는 방법을 보여 줍니다.

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>예제: Java 버전 배포

다음 예제는 미리 구성된 원격 모니터링 솔루션의 표준, Java 버전을 배포하는 방법을 보여 줍니다.

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs 명령 옵션

`pcs` 명령을 실행하여 솔루션을 배포하는 경우 다음을 지정해야 합니다.

- 솔루션에 대한 이름 이 이름은 고유해야 합니다.
- 사용할 Azure 구독입니다.
- 위치
- 마이크로 서비스를 호스팅하는 가상 컴퓨터에 대한 자격 증명 이러한 자격 증명을 사용하여 문제 해결을 위해 가상 컴퓨터에 액세스할 수 있습니다.

`pcs` 명령이 완료되면 미리 구성된 새 솔루션 배포의 URL을 표시합니다. `pcs` 명령 또한 프로비전된 IoT Hub의 이름과 같은 추가 정보로 `{deployment-name}-output.json` 파일을 만듭니다.

명령줄 매개 변수에 대한 자세한 내용은 다음을 실행합니다.

```cmd/sh
pcs -h
```

CLI에 대한 자세한 내용은 [CLI를 사용하는 방법](https://github.com/Azure/pcs-cli/blob/master/README.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 미리 구성된 솔루션 구성
> * 미리 구성된 솔루션 배포
> * 미리 구성된 솔루션에 로그인

이제 원격 모니터링 솔루션을 배포했으며 다음 단계는 [솔루션 대시보드의 기능을 탐색](./iot-suite-remote-monitoring-deploy.md)하는 것입니다.

<!-- Next tutorials in the sequence -->