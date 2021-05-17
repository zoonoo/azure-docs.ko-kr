---
title: Azure Static Web Apps 문제 해결
description: Azure Static Web Apps를 사용할 때 문제를 추적하기 위한 첫 번째 단계입니다.
services: static-web-apps
author: geektrainer
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: chrhar
ms.openlocfilehash: 082c3af694932f4e864b99aa8c2cdc7d1f5fca30
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108210191"
---
# <a name="troubleshooting-deployment-and-runtime-errors"></a>배포 및 런타임 오류 문제 해결

이 문서는 Static Web App에 대한 배포 및 기타 문제 해결을 위한 단계별 가이드를 제공합니다.

## <a name="retrieve-deployment-error-messages"></a>배포 오류 메시지 검색

Azure Static Web Apps 배포 워크플로는 다음 명령을 자동으로 실행하는 Node.js [Oryx 빌드 프로세스](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md#build)를 사용합니다.

```bash
npm install
npm run build # if specified in package.json
npm run build:azure # if specified in package.json
```

이 프로세스에서 발생하는 모든 오류는 GitHub 워크플로 실행에 기록됩니다.

1. Static Web App에 대한 GitHub 리포지토리로 이동합니다.
1. **Actions** 를 선택합니다.

    > [!NOTE]
    > 실패한 모든 워크플로 실행은 녹색 확인 표시가 아니라 빨간색 *X* 로 표시됩니다.

1. validate하고자 하는 워크플로 실행에 대한 링크를 선택합니다.
1. **빌드 및 배포 작업** 을 선택하여 배포 세부 정보를 엽니다.
1. **빌드 및 배포** 를 선택하여 로그를 표시합니다.

    ![Static Web App에 대한 배포 로그 스크린샷](./media/troubleshooting/build-deploy-log.png)

1. 로그 및 오류 메시지를 검토합니다.

    > [!NOTE]
    > 일부 경고 오류 메시지는 *.oryx_prod_node_modules* 및 *작업 영역* 에 대한 노트와 같이 빨간색으로 표시될 수 있습니다. 이는 기본 배포 프로세스의 일부입니다.

패키지를 설치하는 데 실패하거나 다른 문제가 발생하는 경우 로컬에서 `npm` 명령을 실행한 것처럼 여기에 원래 오류 메시지가 표시됩니다.

### <a name="confirm-folder-configuration"></a>폴더 구성 확인

Azure Static Web Apps는 애플리케이션을 호스트하는 데 사용할 폴더를 알고 있어야 합니다. 워크플로가 끝날 때 빌드 프로세스가 이 구성을 확인합니다. 유효성 검사 단계 중에 모든 오류가 기록됩니다.

오류 로그에 다음 오류 메시지 중 하나가 표시되면 워크플로에 대한 폴더 구성이 잘못되었음을 나타내는 것입니다.

| 오류 메시지 | Description |
| --- | --- |
|앱 디렉터리 위치: '/*폴더*'가 잘못되었습니다. 이 디렉터리를 검색할 수 없습니다. | 워크플로가 리포지토리 구조를 반영하는지 확인하세요. |
| 앱 빌드가 '*폴더*' 아티팩트 폴더 생성에 실패했습니다. | `folder` 속성이 워크플로 파일에 올바르게 구성되어 있는지 확인합니다. |
| Api 디렉터리를 지정하지 않았거나 지정된 디렉터리를 찾을 수 없습니다. | 워크플로가 `api` 폴더 값을 정의하지 않으므로 Azure Functions가 생성되지 않습니다. |

워크플로에는 세 가지 지정된 폴더 위치가 있습니다. 이러한 설정이 프로젝트 및 배포 전에 소스 코드를 변환하는 모든 도구와 일치하는지 확인합니다.

| 구성 설정 | Description |
| --- | --- |
| `app_location` | 배포할 소스 코드의 루트 위치입니다. 이 설정은 일반적으로 */* 또는 프로젝트에 대한 JavaScript 및 HTML의 위치입니다. |
| `output_location` | Webpack과 같은 번들러의 빌드 프로세스에서 만든 폴더의 이름입니다. 빌드 프로세스로 이 두 폴더를 만들고 `app_location`에 하위 디렉터리를 만들어야 합니다. |
| `api_location` |Azure Static Web Apps에서 호스트하는 Azure Functions 애플리케이션의 루트 위치입니다. 이는 프로젝트에 대한 모든 Azure Functions (일반적으로 *api*)의 루트 폴더를 가리킵니다. |

> [!NOTE]
> Azure Static Web Apps는 서버리스 코드를 요구하지 않으므로 잘못된 `api_location` 구성으로 생성된 오류 메시지도 성공적으로 빌드할 수 있습니다.

## <a name="review-server-errors"></a>서버 오류 검토

[Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 런타임 오류 메시지를 찾습니다. 만들어진 인스턴스가 없을 경우 [Azure Static Web Apps 모니터링](monitor.md)을 참조하세요. Application Insights 로그는 각각의 오류로 인해 생성된 전체 오류 메시지 및 스택 추적을 기록합니다.

> [!NOTE]
> Application Insights가 설치된 후에 생성되는 오류 메시지만 볼 수 있습니다.

1. Azure Portal 내에서 Static Web App과 연결된 **리소스 그룹** 을 엽니다.
1. Static Web App과 이름이 동일한 Application Insights 인스턴스를 선택합니다(위의 단계를 사용하여 만든 경우).
1. *조사* 에서 **실패** 를 선택합니다.
1. 아래로 스크롤하여 오른쪽의 *Drill into* 로 이동합니다.
1. 오른쪽 아래 모서리에 있는 *Drill into* 단추에 최근에 실패한 작업 수가 표시됩니다.

    ![실패 화면 스크린샷](./media/troubleshooting/app-insights-errors.png)

1. 최근 실패한 작업을 표시하는 패널을 열려면 **x 작업** 단추를 선택합니다.

    ![작업 화면 스크린샷](./media/troubleshooting/app-insights-operations.png)

1. 목록에서 하나를 선택하여 오류를 검색합니다.

    ![오류 세부 정보 화면 스크린샷](./media/troubleshooting/app-insights-details.png)

## <a name="environment-variables"></a>환경 변수

많은 웹 애플리케이션은 환경 변수를 사용하여 환경과 관련되었거나 중요한 설정을 저장합니다. Azure Static Web Apps는 애플리케이션 설정을 통해 환경 변수를 지원합니다.

애플리케이션 설정은 애플리케이션에 대한 환경 변수를 설정하는 키/값 쌍입니다. 환경 변수에 액세스하는 경우와 동일한 구문을 사용 하여 애플리케이션에 변수를 사용할 수 있습니다.

배포할 때 모든 환경 변수가 애플리케이션 설정으로 설정되어 있는지 확인합니다.

1. Azure Portal의 Static Web App을 엽니다.
1. **Configuration(구성)** 을 선택합니다. *구성* 화면에 모든 애플리케이션 설정 목록이 표시됩니다.

    ![Static Web App 구성 화면 스크린샷](media/troubleshooting/app-settings.png)

새 변수를 추가하려면 다음 단계를 따릅니다.

1. **추가** 를 선택합니다.
1. **이름** 을 설정합니다.
1. **값** 을 설정합니다.
1. **확인** 을 선택합니다.
1. **저장** 을 선택합니다.
