---
title: JavaScript 응용 프로그램에 대 한 소스 맵 지원-Azure Monitor Application Insights
description: Application Insights를 사용 하 여 사용자 고유의 저장소 계정 Blob 컨테이너에 소스 맵을 업로드 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5b15492a36032a53ac81929eb55bce0bc70e040c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335134"
---
# <a name="source-map-support-for-javascript-applications"></a>JavaScript 응용 프로그램에 대 한 소스 맵 지원

Application Insights는 사용자 고유의 저장소 계정 Blob 컨테이너에 대 한 원본 맵의 업로드를 지원 합니다.
원본 맵은 종단 간 트랜잭션 세부 정보 페이지에서 찾은 호출 스택을 제거 하는 데 사용할 수 있습니다. [JAVASCRIPT sdk][ApplicationInsights-JS] 또는 [Node.js SDK][ApplicationInsights-Node.js] 에서 전송 하는 모든 예외는 원본 맵으로 사용 하지 않도록 설정할 수 있습니다.

![저장소 계정과 연결 하 여 호출 스택 제거](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>새 저장소 계정 및 Blob 컨테이너 만들기

기존 저장소 계정 또는 blob 컨테이너가 이미 있는 경우이 단계를 건너뛸 수 있습니다.

1. [새 스토리지 계정 만들기][create storage account]
2. 저장소 계정 내에 [blob 컨테이너를 만듭니다][create blob container] . 원본 맵에 공개적으로 액세스할 수 없도록 하려면 "공용 액세스 수준"을로 설정 해야 `Private` 합니다.

> [!div class="mx-imgBorder"]
>![컨테이너 액세스 수준을 개인으로 설정 해야 합니다.](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Blob 컨테이너에 소스 맵 푸시

구성 된 Blob 컨테이너에 원본 맵을 자동으로 업로드 하도록 구성 하 여 연속 배포 파이프라인을 저장소 계정과 통합 해야 합니다.

소스 맵은 & 배포 된 것과 동일한 폴더 구조를 사용 하 여 Blob Storage 컨테이너에 업로드할 수 있습니다. 일반적인 사용 사례는 배포 폴더를 해당 버전 (예:)으로 접두사로 사용 하는 것 `1.2.3/static/js/main.js` 입니다. 이라는 Azure Blob 컨테이너를 통해 unminifying 때 `sourcemaps` 에 있는 원본 맵을 인출 하려고 시도 `sourcemaps/1.2.3/static/js/main.js.map` 합니다.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Azure Pipelines를 통해 소스 맵 업로드 (권장)

Azure Pipelines를 사용 하 여 응용 프로그램을 지속적으로 빌드 및 배포 하는 경우 [Azure 파일 복사][azure file copy] 작업을 파이프라인에 추가 하 여 원본 맵을 자동으로 업로드 합니다.

> [!div class="mx-imgBorder"]
> ![파이프라인에 Azure 파일 복사 작업을 추가 하 여 원본 맵을 Azure Blob Storage에 업로드 합니다.](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>원본 맵 저장소 계정을 사용 하 여 Application Insights 리소스 구성

### <a name="from-the-end-to-end-transaction-details-page"></a>종단 간 트랜잭션 세부 정보 페이지에서

종단 간 트랜잭션 세부 정보 탭에서 *취소를 클릭할* 수 있습니다. 그러면 리소스가 구성 되지 않은 경우 구성 프롬프트가 표시 됩니다.

1. 포털에서 확인 되지 않은 예외에 대 한 세부 정보를 확인 합니다.
2. 취소를 *클릭* 합니다.
3. 리소스가 구성 되지 않은 경우 메시지를 표시 하 고 사용자에 게를 구성 하 라는 메시지가 표시 됩니다.

### <a name="from-the-properties-page"></a>속성 페이지에서

Application Insights 리소스에 연결 된 저장소 계정 또는 Blob 컨테이너를 구성 하거나 변경 하려는 경우 Application Insights 리소스의 *속성* 탭을 확인 하 여 수행할 수 있습니다.

1. Application Insights 리소스의 *속성* 탭으로 이동 합니다.
2. *원본 맵 blob 컨테이너 변경*을 클릭 합니다.
3. 원본 맵 컨테이너와 다른 Blob 컨테이너를 선택 합니다.
4. `Apply`을 클릭합니다.

> [!div class="mx-imgBorder"]
> ![속성 블레이드로 이동 하 여 선택한 Azure Blob 컨테이너를 다시 구성 합니다.](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>문제 해결

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Blob 컨테이너에서 필요한 RBAC (역할 기반 액세스 제어) 설정

이 기능을 사용 하는 포털의 모든 사용자는 적어도 Blob 컨테이너에 [저장소 Blob 데이터 판독기][storage blob data reader] 로 할당 되어야 합니다. 이 기능을 통해 소스 맵을 사용 하는 다른 모든 사용자에 게이 역할을 할당 해야 합니다.

> [!NOTE]
> 컨테이너를 만든 방법에 따라 사용자 또는 팀에 자동으로 할당 되지 않았을 수 있습니다.

### <a name="source-map-not-found"></a>원본 맵을 찾을 수 없습니다.

1. 해당 하는 원본 맵이 올바른 blob 컨테이너에 업로드 되었는지 확인 합니다.
2. 원본 맵 파일에 매핑되는 JavaScript 파일의 이름이로 지정 되어 있는지 확인 `.map` 합니다.
    - 예를 들어는 `/static/js/main.4e2ca5fa.chunk.js` 이라는 blob을 검색 합니다. `main.4e2ca5fa.chunk.js.map`
3. 브라우저의 콘솔에서 오류를 기록 하 고 있는지 확인 합니다. 지원 티켓에이를 포함 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 파일 복사 작업](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
