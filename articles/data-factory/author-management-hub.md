---
title: 관리 허브
description: Azure Data Factory 관리 허브에서 연결, 소스 제어 구성 및 전역 제작 속성 관리
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.date: 06/02/2020
ms.openlocfilehash: 308d19fde78edacebb168b8d4e459169338acc41
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84345944"
---
# <a name="management-hub-in-azure-data-factory"></a>Azure Data Factory의 관리 허브

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory UX의 *관리* 탭에서 액세스할 수 있는 관리 허브는 데이터 팩터리에 대 한 전역 관리 작업을 호스트 하는 포털입니다. 여기에서 데이터 저장소 및 외부 계산, 소스 제어 구성 및 트리거 설정에 대 한 연결을 관리할 수 있습니다.

## <a name="manage-connections"></a>연결 관리

### <a name="linked-services"></a>연결된 서비스

연결 된 서비스는 외부 데이터 저장소 및 계산 환경에 연결 하는 Azure Data Factory에 대 한 연결 정보를 정의 합니다. 자세한 내용은 [연결 된 서비스 개념](concepts-linked-services.md)을 참조 하세요. 연결 된 서비스 만들기, 편집 및 삭제는 관리 허브에서 수행 됩니다.

![연결 된 서비스 관리](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>통합 런타임

Integration runtime은 여러 네트워크 환경에서 데이터 통합 기능을 제공 하기 위해 Azure Data Factory에서 사용 하는 계산 인프라입니다. 자세한 내용은 [integration runtime 개념](concepts-integration-runtime.md)에 대해 알아보세요. 관리 허브에서 통합 런타임을 만들고, 삭제 하 고, 모니터링할 수 있습니다.

![통합 런타임 관리](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>원본 제어 관리

### <a name="git-configuration"></a>Git 구성

관리 허브에서 구성 된 git 리포지토리 설정을 보고 편집 합니다. 자세한 내용은 [Azure Data Factory의 원본 제어](source-control.md)에 대해 알아보세요.

![Git 리포지토리 관리](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>매개 변수화 템플릿

공동 작업 분기에서 게시할 때 생성 된 리소스 관리자 템플릿 매개 변수를 재정의 하려면 사용자 지정 매개 변수 파일을 생성 하거나 편집할 수 있습니다. 자세한 내용은 [리소스 관리자 템플릿에서 사용자 지정 매개 변수를 사용](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)하는 방법을 알아봅니다. 매개 변수화 템플릿은 git 리포지토리에서 작업 하는 경우에만 사용할 수 있습니다. 작업 분기에 *arm-template-parameters-definition.js파일에* 없는 경우 기본 템플릿을 편집 하면 해당 파일이 생성 됩니다.

![사용자 지정 매개 변수 관리](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>제작 관리

### <a name="triggers"></a>트리거

트리거는 파이프라인 실행을 시작 해야 하는 시기를 결정 합니다. 현재 트리거는 벽 시계 일정에 있거나, 정기적으로 작동 하거나, 이벤트에 따라 달라질 수 있습니다. 자세한 내용은 [트리거 실행](concepts-pipeline-execution-triggers.md#trigger-execution)에 대해 알아봅니다. 관리 허브에서 트리거의 현재 상태를 만들거나, 편집 하거나, 삭제 하거나, 볼 수 있습니다.

![사용자 지정 매개 변수 관리](media/author-management-hub/management-hub-triggers.png)

## <a name="next-steps"></a>다음 단계

ADF에 [git 리포지토리를 구성](source-control.md) 하는 방법에 대해 알아봅니다.


