---
title: Azure Portal에서 함수 앱 만들기
description: Azure에서 포털을 통해 새 함수 앱을 만듭니다.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 086a543e75d083094d4dfa789e71afaba5da6d6e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368777"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Azure Portal에서 함수 앱 만들기

이 항목에서는 Azure Functions를 사용 하 여 Azure Portal에서 함수 앱을 만드는 방법을 보여 줍니다. 함수 앱은 개별 함수의 실행을 호스팅하는 컨테이너입니다. 

## <a name="create-a-function-app"></a>함수 앱 만들기

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

함수 앱을 만든 후에 하나 이상의 서로 다른 언어로 개별 함수를 만들 수 있습니다. [포털을 사용하여](functions-create-first-azure-function.md#create-function), [연속 배포](functions-continuous-deployment.md) 또는 [FTP를 사용하여 업로드](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)하여 함수를 만듭니다.

## <a name="service-plans"></a>서비스 계획

Azure Functions에는 소비 계획, 프리미엄 계획 및 전용 (App Service) 계획과 같은 세 가지 서비스 계획이 있습니다. 함수 앱을 만들 때 서비스 계획을 선택 해야 하며 이후에는 변경할 수 없습니다. 자세한 내용은 [Azure Functions 호스팅 계획 선택](functions-scale.md)을 참조하세요.

전용 (App Service) 계획에서 JavaScript 함수를 실행 하려는 경우 코어 수가 더 작은 계획을 선택 해야 합니다. 자세한 내용은 [함수에 대한 JavaScript 참조](functions-reference-node.md#choose-single-vcpu-app-service-plans)를 참조하세요.

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

함수 앱을 만들 때 Blob, 큐 및 테이블 저장소를 지 원하는 범용 Azure Storage 계정을 만들거나 연결 해야 합니다. 내부적으로 함수는 트리거 관리 및 함수 실행 로깅 등의 작업을 위해 Storage를 사용합니다. Blob 전용 스토리지 계정, Azure Premium Storage 및 ZRS 복제를 포함한 범용 스토리지 계정과 같은 일부 스토리지 계정은 큐 및 테이블을 지원하지 않습니다. 이러한 계정은 함수 앱을 만들 때 Storage 계정 블레이드에서 필터링됩니다.

>[!NOTE]
>소비 호스팅 계획을 사용할 경우 함수 코드 및 바인딩 구성 파일은 기본 스토리지 계정의 Azure File Storage에 저장됩니다. 기본 스토리지 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#azure-storage-services)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

Azure Portal을 통해 쉽게 함수를 만들고 사용해 볼 수 있습니다. [로컬 개발](functions-develop-local.md)을 권장합니다. 포털에서 함수 앱을 만든 후에도 함수를 추가해야 합니다. 

> [!div class="nextstepaction"]
> [HTTP 트리거 함수 추가](functions-create-first-azure-function.md#create-function)
