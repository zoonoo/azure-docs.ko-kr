---
title: Azure Data Factory에서 데이터 팩터리 복사 또는 복제
description: Azure Data Factory에서 데이터 팩터리를 복사 또는 복제하는 방법 알아보기
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 304c39f4b6f7852068d4e72adfad2d41eeefc26c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85552964"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Azure Data Factory에서 데이터 팩터리 복사 또는 복제

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Factory에서 데이터 팩터리를 복사 또는 복제하는 방법을 설명합니다.

## <a name="use-cases-for-cloning-a-data-factory"></a>데이터 팩터리 복제 사용 사례

다음은 데이터 팩터리를 복사 또는 복제하는 것이 유용할 수 있는 몇 가지 상황입니다.

- **Data Factory** 를 새 지역으로 이동 합니다. Data Factory을 다른 지역으로 이동 하려면 대상 지역에 복사본을 만들고 기존 항목을 삭제 하는 것이 가장 좋습니다.

- **Data Factory 이름 바꾸기** Azure는 리소스 이름 바꾸기를 지원하지 않습니다. 데이터 팩터리의 이름을 변경 하려는 경우 다른 이름으로 데이터 팩터리를 복제 하 고 기존 데이터 팩터리를 삭제할 수 있습니다.

- 디버그 기능으로 충분하지 않을 경우 **변경 내용 디버그**. 대부분의 시나리오에서는 [Debug](iterative-development-debugging.md)를 사용할 수 있습니다. 다른 방법으로는 복제 된 샌드박스 환경에서 변경 내용을 테스트 하는 것이 더 좋습니다. 예를 들어, 파일 도착 시 트리거를 실행 하는 경우 매개 변수가 있는 ETL 파이프라인이 작동 하는 방식과 연속 기간을 초과 하는 경우에는 디버그 만으로 쉽게 테스트할 수 없습니다. 이러한 경우 실험을 위해 샌드박스 환경을 복제 하는 것이 좋습니다. Azure Data Factory는 주로 실행 횟수에 따라 요금을 청구 하기 때문에 두 번째 팩터리에서 추가 요금이 발생 하지 않습니다.

## <a name="how-to-clone-a-data-factory"></a>데이터 팩터리 복제 방법

1. 필수 구성 요소로, 먼저 Azure Portal에서 대상 데이터 팩터리를 만들어야 합니다.

1. GIT 모드에 있는 경우:
    1. 포털에서 게시할 때마다 팩터리의 리소스 관리자 템플릿이 adf 게시 분기에 GIT에 저장 됩니다. \_
    1. 새 팩터리를 _동일한_ 리포지토리에 연결 하 고 adf \_ 게시 분기에서 빌드합니다. 파이프라인, 데이터 집합 및 트리거와 같은 리소스는 다음을 통해 수행 됩니다.

1. 라이브 모드인 경우:
    1. Data Factory UI를 사용 하 여 데이터 팩터리의 전체 페이로드를 리소스 관리자 템플릿 파일 및 매개 변수 파일로 내보낼 수 있습니다. 이 파일은 포털의 **ARM 템플릿 \ 리소스 관리자 템플릿 내보내기** 단추에서 액세스할 수 있습니다.
    1. 매개 변수 파일을 적절 하 게 변경 하 고 새 팩터리에 대 한 새 값을 교환할 수 있습니다.
    1. 다음으로 표준 리소스 관리자 템플릿 배포 방법을 통해 배포할 수 있습니다.

1. 원본 팩터리에 SelfHosted IntegrationRuntime가 있는 경우 대상 팩터리에 동일한 이름으로 precreate 해야 합니다. 서로 다른 팩터리 간에 SelfHosted Integration Runtime를 공유 하려면 SelfHosted IR 공유에서 [여기](create-shared-self-hosted-integration-runtime-powershell.md) 에 게시 된 패턴을 사용할 수 있습니다.

1. 보안상의 이유로 생성 된 리소스 관리자 템플릿에는 연결 된 서비스에 대 한 암호와 같은 암호 정보가 포함 되지 않습니다. 따라서 자격 증명을 배포 매개 변수로 제공 해야 합니다. 설정에 자격 증명을 수동으로 입력 하는 것이 바람직하지 않은 경우 대신 Azure Key Vault에서 연결 문자열과 암호를 검색 하는 것이 좋습니다. [자세히 보기](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>다음 단계

[Azure Data Factory UI를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)에서 Azure Portal을 통해 데이터 팩터리를 만들기 위한 지침을 검토하세요.
