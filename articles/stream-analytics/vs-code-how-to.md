---
title: Visual Studio Code (미리 보기)를 사용 하 여 Azure Stream Analytics 살펴보기
description: 이 문서에서는 목록 작업 및 보기 작업 엔터티를 로컬 프로젝트에 Azure Stream Analytics 작업을 내보내기 및 Stream Analytics 작업에 대 한 CI/CD 파이프라인을 설정 하는 방법을 보여 줍니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079212"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Visual Studio Code (미리 보기)를 사용 하 여 Azure Stream Analytics 살펴보기

Azure Stream Analytics Visual Studio Code 확장에 대 한 Stream Analytics 작업을 관리 하기 위한 개발자는 간단한 환경을 제공 합니다. Azure Stream Analytics 확장을 사용 하 여 다음을 수행할 수 있습니다.

- [만들](quick-create-vs-code.md), 시작 및 중지 작업
- 로컬 프로젝트에 기존 작업 내보내기
- 쿼리를 로컬로 실행
- CI/CD 파이프라인 설정

## <a name="export-a-job-to-a-local-project"></a>로컬 프로젝트 작업을 내보내려면

작업을 로컬 프로젝트 내보내려면에서 내보내려는 작업을 찾을 합니다 **Stream Analytics 탐색기** Visual Studio code에서. 그런 다음 프로젝트에 대 한 폴더를 선택 합니다. 프로젝트를 선택 하면 폴더를 내보내고 계속 Visual Studio Code에서 작업을 관리할 수 있습니다. Visual Studio Code를 사용 하 여 Stream Analytics 작업을 관리 하는 방법은 Visual Studio Code를 참조 하세요 [퀵 스타트](quick-create-vs-code.md)합니다.

![Visual Studio Code에서 ASA 작업 내보내기](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>쿼리를 로컬로 실행

로컬로 샘플 데이터로 Stream Analytics 작업을 테스트 하려면 Visual Studio Code 용 Azure Stream Analytics 확장을 사용할 수 있습니다.

1. Stream Analytics 작업을 만든 후 사용 하 여 **Ctrl + Shift + P** 를 명령 팔레트를 엽니다. 입력 한 다음 선택한 **ASA: 입력 추가**합니다.

    ![Visual Studio code에서 ASA 입력 추가](./media/vs-code-how-to/add-input.png)

2. 선택 **로컬 입력**합니다.

    ![Visual Studio code에서 ASA 로컬 입력 추가](./media/vs-code-how-to/add-local-input.png)

3. 선택 **+ 새 로컬 입력**합니다.

    ![새 ASA 로컬 Visual Studio code에서 입력 추가](./media/vs-code-how-to/add-new-local-input.png)

4. 쿼리에서 동일한 입력된 별칭을 입력 합니다.

    ![새 ASA 로컬 입력된 별칭 추가](./media/vs-code-how-to/new-local-input-alias.png)

5. 에 **LocalInput_DefaultLocalStream.json** 파일을 로컬 데이터 파일에 있는 파일 경로 입력 합니다.

    ![Visual Studio에서 로컬 파일 경로를 입력 하십시오.](./media/vs-code-how-to/local-file-path.png)

6. 선택한 쿼리 편집기로 돌아가서 **로컬로 실행**합니다.

    ![쿼리 편집기에서 로컬로 실행을 선택](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>CI/CD 파이프라인 설정

지속적인 통합 및 배포를 사용 하 여 Azure Stream Analytics 작업에 대해 설정할 수 있습니다.는 **asa cicd 도구** NPM 패키지 있습니다. NPM 패키지를 Stream Analytics Visual Studio 코드 프로젝트의 자동 배포를 위한 도구를 제공합니다. Visual Studio Code를 설치 하지 않고 Windows, macOS 및 Linux에서 사용할 수 있습니다.

했으면 [패키지를 다운로드 한](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), 다음 명령을 사용 하 여 Azure Resource Manager 템플릿 출력 합니다. 경우는 **outputPath** 지정 하지 않으면 서식 파일에 배치 됩니다 합니다 **배포** 프로젝트의 폴더 **bin** 폴더입니다.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code (미리 보기)에서 클라우드 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)