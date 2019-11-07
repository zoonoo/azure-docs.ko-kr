---
title: Azure 스프링 클라우드에서 영구 저장소를 사용 하는 방법 | Microsoft Docs
description: Azure 스프링 클라우드에서 영구 저장소를 사용 하는 방법
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: d70e7ff747b80b661e848f1c208f0d1c2c928248
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607779"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 영구 저장소를 사용 하는 방법

Azure 스프링 클라우드는 응용 프로그램에 영구 및 임시의 두 가지 저장소 유형을 제공 합니다.  Azure 스프링 클라우드는 각 응용 프로그램 인스턴스에 대해 기본적으로 임시 저장소를 사용 하도록 설정 합니다. 임시 저장소는 `/tmp`기본 탑재 경로를 사용 하는 5GB로 제한 됩니다.

> [!WARNING]
> 응용 프로그램 인스턴스를 다시 시작 하면 연결 된 임시 저장소가 영구적으로 삭제 됩니다.

영구 저장소는 응용 프로그램당 할당 된 Azure에서 관리 하는 파일 공유 컨테이너입니다. 영구적 저장소에 저장 된 데이터는 응용 프로그램의 모든 인스턴스에서 공유 됩니다. Azure 스프링 클라우드 서비스 인스턴스는 영구 디스크를 사용 하는 응용 프로그램을 최대 10 개까지 포함할 수 있습니다. 각 응용 프로그램은 50GB의 영구 저장소를 수신 합니다. 영구 저장소에 대 한 기본 탑재 경로를 `/persistent`합니다.

> [!WARNING]
> 영구 저장소를 *사용 하지 않도록 설정* 하면 해당 응용 프로그램에 대 한 저장소의 할당이 취소 됩니다.  해당 저장소 계정에 있는 모든 데이터가 손실 됩니다. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Azure Portal를 사용 하 여 영구 저장소 사용

1. Azure Portal의 홈 화면에서 **모든 리소스**를 선택 합니다.

     >![모든 리소스 아이콘 찾기](media/portal-all-resources.jpg)

1. 영구 저장소를 필요로 하는 Azure 스프링 클라우드 리소스를 찾아 선택 합니다.  이 예제에서는 응용 프로그램을 *jpspring*이라고 합니다.

    > ![Applicationb 찾기](media/select-service.jpg)

1. **설정** 제목 아래에서 **앱**을 선택 합니다.

1. 스프링 클라우드 서비스는 테이블에 표시 됩니다.  영구 저장소를 추가 하려는 서비스를 선택 합니다.  이 예제에서는 **게이트웨이** 서비스를 선택 합니다.

    > ![서비스 선택](media/select-gateway.jpg)

1. 서비스의 구성 블레이드에서 **구성** 을 선택 합니다.

1. **영구 저장소** 탭을 선택 하 고 영구 저장소를 사용 하도록 설정 합니다.

    > ![영구 저장소 사용](media/enable-persistent-storage.jpg)

영구 저장소를 사용 하는 경우이 페이지에 크기와 경로가 모두 표시 됩니다.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Azure CLI를 사용 하 여 영구 저장소 수정

필요한 경우 Azure CLI에 대 한 스프링 클라우드 확장을 설치 합니다.

```azurecli
az extension add --name spring-cloud
```

영구 디스크를 사용 하도록 설정 된 앱을 만듭니다.
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

기존 앱에서 영구 저장소를 사용 하도록 설정 합니다.

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

기존 앱에서 영구 저장소를 사용 하지 않도록 설정:

> [!WARNING]
> 영구 저장소를 사용 하지 않도록 설정 하면 해당 응용 프로그램에 저장 된 데이터가 영구적으로 손실 되어 해당 응용 프로그램에 대 한 저장소가 할당 취소 됩니다. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>다음 단계

응용 프로그램 [및 서비스 할당량](spring-cloud-quotas.md)에 대해 알아보거나 [응용 프로그램 크기를 수동으로 조정](spring-cloud-tutorial-scale-manual.md)하는 방법을 알아보세요.