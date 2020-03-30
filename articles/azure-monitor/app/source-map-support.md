---
title: 자바 스크립트 응용 프로그램에 대한 소스지도 지원 - Azure 모니터 응용 프로그램 인사이트
description: Application Insights를 사용하여 소스 맵을 자신의 저장소 계정 Blob 컨테이너에 업로드하는 방법을 알아봅니다.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474886"
---
# <a name="source-map-support-for-javascript-applications"></a>자바 스크립트 응용 프로그램에 대한 소스지도 지원

Application Insights는 자체 저장소 계정 Blob 컨테이너에 소스 맵을 업로드하는 것을 지원합니다.
소스 맵을 사용하여 트랜잭션 세부 정보 페이지 끝까지 끝에 있는 호출 스택을 최소화할 수 있습니다. [JavaScript SDK][ApplicationInsights-JS] 또는 [Node.js SDK에서][ApplicationInsights-Node.js] 보낸 예외는 소스 맵으로 취소할 수 있습니다.

![저장소 계정과 연결하여 통화 스택 을 최소화](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>새 저장소 계정 및 Blob 컨테이너 만들기

기존 저장소 계정 또는 Blob 컨테이너가 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

1. [새 스토리지 계정 만들기][create storage account]
2. 저장소 계정 내에 [Blob 컨테이너를 만듭니다.][create blob container] 소스 맵에 공개적으로 액세스할 수 `Private`없도록 하려면 "공용 액세스 수준"을 로 설정해야 합니다.

> [!div class="mx-imgBorder"]
>![컨테이너 액세스 수준을 Private으로 설정해야 합니다.](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>소스 맵을 Blob 컨테이너로 푸시

소스 맵을 구성된 Blob 컨테이너에 자동으로 업로드하도록 구성하여 연속 배포 파이프라인을 저장소 계정과 통합해야 합니다. 소스 맵을 Blob 컨테이너의 하위 폴더에 업로드해서는 안 됩니다. 현재 소스 맵은 루트 폴더에서만 가져옵니다.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Azure 파이프라인을 통해 소스 맵 업로드(권장)

Azure 파이프라인을 사용하여 응용 프로그램을 지속적으로 빌드하고 배포하는 경우 파이프라인에 [Azure File Copy][azure file copy] 작업을 추가하여 원본 맵을 자동으로 업로드합니다.

> [!div class="mx-imgBorder"]
> ![파이프라인에 Azure 파일 복사 작업을 추가하여 Azure Blob 저장소에 소스 맵을 업로드합니다.](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>소스 맵 저장소 계정으로 응용 프로그램 인사이트 리소스 구성

### <a name="from-the-end-to-end-transaction-details-page"></a>종단 간 트랜잭션 세부 정보 페이지에서

종단 간 트랜잭션 세부 정보 탭에서 *Unminify를* 클릭하면 리소스가 구성되지 않은 경우 구성하라는 메시지가 표시됩니다.

1. 포털에서 minified된 예외의 세부 정보를 봅니다.
2. *언미니어드를* 클릭
3. 리소스가 구성되지 않은 경우 구성하라는 메시지가 표시됩니다.

### <a name="from-the-properties-page"></a>속성 페이지에서

응용 프로그램 인사이트 리소스에 연결된 저장소 계정 또는 Blob 컨테이너를 구성하거나 변경하려는 경우 응용 프로그램 인사이트 리소스의 *속성* 탭을 참조하여 구성할 수 있습니다.

1. 응용 프로그램 인사이트 리소스의 *속성* 탭으로 이동합니다.
2. 소스 *맵 Blob 컨테이너 변경을*클릭합니다.
3. 소스 맵 컨테이너로 다른 Blob 컨테이너를 선택합니다.
4. `Apply`을 클릭합니다.

> [!div class="mx-imgBorder"]
> ![속성 블레이드로 이동하여 선택한 Azure Blob 컨테이너를 다시 구성합니다.](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>문제 해결

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Blob 컨테이너의 필수 역할 기반 액세스 제어(RBAC) 설정

이 기능을 사용하는 포털의 모든 사용자는 최소한 Blob 컨테이너에 [저장소 Blob 데이터 판독기로][storage blob data reader] 할당되어야 합니다. 이 기능을 통해 소스 맵을 사용할 다른 사람에게 이 역할을 할당해야 합니다.

> [!NOTE]
> 컨테이너가 생성된 방법에 따라 사용자 또는 팀에 자동으로 할당되지 않았을 수 있습니다.

### <a name="source-map-not-found"></a>소스 맵을 찾을 수 없습니다.

1. 해당 소스 맵이 올바른 Blob 컨테이너에 업로드되었는지 확인합니다.
2. 소스 맵 파일의 이름이 JavaScript 파일의 이름을 따서 명명되었는지 `.map`확인합니다.
    - 예를 들어, `/static/js/main.4e2ca5fa.chunk.js` 명명된 Blob을 검색합니다.`main.4e2ca5fa.chunk.js.map`
3. 브라우저의 콘솔을 확인하여 오류가 기록되고 있는지 확인합니다. 지원 티켓에 이 것을 포함시면 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure 파일 복사 작업](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme