---
title: "Azure CLI 2.0을 사용하여 Service Fabric 응용 프로그램 관리"
description: "Service Fabric 클러스터에서 Azure CLI 2.0을 사용하여 응용 프로그램을 배포하고 제거하는 프로세스 설명"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Service Fabric 응용 프로그램 관리

이 설명서에 따라 Service Fabric 클러스터에서 실행 중인 응용 프로그램을 만들고 삭제합니다.

## <a name="prerequisites"></a>필수 조건

Azure CLI 2.0을 설치하고 Service Fabric 클러스터를 선택해야 합니다. 자세한 정보는 [Azure CLI 2.0 시작 설명서](service-fabric-azure-cli-2-0.md)에서 찾을 수 있습니다.

배포할 준비가 된 Service Fabric 응용 프로그램 패키지도 있어야 합니다. 응용 프로그램 작성 및 패키지 방법에 대한 자세한 정보는 [응용 프로그램 모델 설명서](service-fabric-application-model.md)에서 찾을 수 있습니다.

## <a name="overview"></a>개요

새 응용 프로그램 배포는 4단계로 구성되어 있습니다.

1. Service Fabric 이미지 저장소에 응용 프로그램 패키지 업로드
1. 응용 프로그램 유형 프로비전
1. 응용 프로그램 지정 및 만들기
1. 서비스 지정 및 만들기

기존 응용 프로그램 제거는 3단계로 이루어져 있습니다.

1. 응용 프로그램 삭제
1. 관련된 응용 프로그램 유형 프로비전 해제
1. 이미지 저장소 콘텐츠 삭제

## <a name="deploy-a-new-application"></a>새 응용 프로그램 배포

새 응용 프로그램을 배포하려면 다음 단계를 수행합니다.

### <a name="upload-a-new-application-package-to-the-image-store"></a>이미지 저장소에 새 응용 프로그램 패키지 업로드

응용 프로그램을 만들기 전에 Service Fabric 이미지 저장소에 응용 프로그램 패키지를 업로드해야 합니다.
그런 다음 응용 프로그램 패키지가 `app_package_dir` 디렉터리에 있다고 가정합니다. 다음 명령을 사용하여 디렉터리를 업로드합니다.

```azurecli
az sf application upload --path ~/app_package_dir
```

대형 응용 프로그램 패키지의 경우 `--show-progress` 옵션을 지정하여 업로드 진행률을 표시할 수 있습니다.

### <a name="provision-application-type"></a>응용 프로그램 유형 프로비전

업로드가 완료되면 응용 프로그램을 프로비전해야 합니다. 응용 프로그램을 프로비전하려면 다음 명령을 사용합니다.

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

`application-type-build-path`는 이전에 업로드된 응용 프로그램 패키지가 포함된 디렉터리의 이름과 동일합니다.

### <a name="create-application-from-application-type"></a>응용 프로그램 유형에서 응용 프로그램 만들기

응용 프로그램이 프로비전된 후에는 다음 명령을 사용하여 응용 프로그램 이름을 지정하고 만들 수 있습니다.

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

여기에서 `app-name`은 응용 프로그램 인스턴스에 지정할 이름입니다. 다른 매개 변수는 이전에 프로비전된 응용 프로그램 매니페스트에서 찾을 수 있습니다.

응용 프로그램 이름은 `fabric:/` 접두사로 시작해야 합니다.

### <a name="create-services-for-the-new-application"></a>새 응용 프로그램에 대한 서비스 만들기

응용 프로그램이 만들어진 후에는 응용 프로그램에서 서비스를 만들 수 있습니다. 이 예의 경우 응용 프로그램에서 새 상태 비저장 서비스를 만듭니다. 응용 프로그램에서 만들 수 있는 서비스는 이전에 프로비전된 응용 프로그램 패키지 내의 서비스 매니페스트에 정의됩니다.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>응용 프로그램 만들기 및 상태 확인

응용 프로그램 및 서비스가 성공적으로 배포되었는지 확인하려면 다음 명령을 사용하여 응용 프로그램 및 서비스가 나열되어 있는지 확인할 수 있습니다.

```azurecli
az sf application list
az sf service list --application-list TestApp
```

서비스는 정상 상태인지 확인하려면 유사한 명령을 사용하여 서비스와 응용 프로그램의 상태를 검색합니다.

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

정상 서비스 및 응용 프로그램에서는 `HealthState` 값이 `Ok`이어야 합니다.

## <a name="remove-an-existing-application"></a>기존 응용 프로그램 제거

응용 프로그램을 제거하려면 다음 단계를 따릅니다.

### <a name="delete-the-application"></a>응용 프로그램 삭제

다음 명령을 실행하여 응용 프로그램을 삭제합니다.

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>응용 프로그램 유형 프로비전 해제

응용 프로그램이 삭제된 후 응용 프로그램 유형이 더 이상 필요하지 않은 경우 프로비전 해제될 수 있습니다. 다음 명령을 사용하여 응용 프로그램 유형을 프로비전 해제합니다.

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

여기서 유형 이름 및 유형 버전은 이전에 프로비전된 응용 프로그램 매니페스트의 이름 및 버전과 일치해야 합니다.

### <a name="delete-application-package"></a>응용 프로그램 패키지 삭제

응용 프로그램 유형이 프로비전 해제된 후 응용 프로그램 패키지가 더 이상 필요하지 않은 경우 이미지 저장소에서 삭제될 수 있습니다. 응용 프로그램 패키지를 삭제하면 디스크 공간을 확보하는 데 도움이 됩니다. 다음 명령을 사용하여 이미지 저장소에서 응용 프로그램 패키지를 삭제합니다.

```azurecli
az sf application package-delete --content-path app_package_dir
```

여기서 `content-path`는 응용 프로그램을 만들 때 처음에 업로드된 디렉터리와 이름이 동일해야 합니다.

## <a name="related-articles"></a>관련 문서

* [Service Fabric 및 Azure CLI 2.0 시작](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI 시작](service-fabric-azure-cli.md)

