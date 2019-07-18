---
title: Azure에서 사용자 지정 작업 및 리소스 만들기
description: 이 자습서에서는 Azure Resource Manager에서 사용자 지정 작업 및 리소스를 만드는 방법과 Azure Resource Manager 템플릿, Azure CLI, Azure Policy 및 Activity Log에 대한 사용자 지정 워크플로에 통합하는 방법을 살펴봅니다.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799192"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Azure에서 사용자 지정 작업 및 리소스 만들기

사용자 지정 공급 기업을 사용하면 Azure에서 워크플로를 사용자 지정할 수 있습니다. 사용자 지정 공급 기업은 Azure와 `endpoint` 사이의 계약입니다. 이를 통해 새로운 사용자 정의 API를 Azure Resource Manager에 추가하여 새로운 배포 및 관리 기능을 사용할 수 있습니다. 이 자습서에서는 Azure에 새 작업 및 리소스를 추가하는 방법과 통합하는 방법에 대한 간단한 예제를 살펴봅니다.

이 자습서는 다음과 같은 단계로 나뉩니다.

- Azure 사용자 지정 공급 기업을 위한 Azure Functions 설정
- 사용자 지정 공급 기업을 위한 RESTful 엔드포인트 작성
- 사용자 공급 기업 만들기 및 활용

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Azure 사용자 지정 공급 기업을 위한 Azure Functions 설정

자습서의 이 부분에서는 사용자 지정 공급 기업과 작업하도록 Azure Function을 설정하는 방법에 대해 자세히 설명합니다. 사용자 지정 공급 기업은 공용 URL을 사용할 수 있습니다.

- [Azure 사용자 지정 공급 기업을 위한 Azure Functions 설정](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>사용자 지정 공급 기업을 위한 RESTful 엔드포인트 작성

자습서의 이 부분에서는 사용자 지정 공급 기업을 위한 RESTful 엔드포인트를 작성하는 데 관해 자세히 설명합니다.

- [사용자 지정 공급 기업을 위한 RESTful 엔드포인트 작성](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>사용자 공급 기업 만들기 및 활용

자습서의 이 부분에서는 사용자 공급 기업을 만들고 해당 사용자 지정 작업 및 리소스를 사용하는 방법을 자세히 알아봅니다.

- [Azure 사용자 지정 공급 기업 만들기 및 활용](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업 및 빌드하는 방법에 대해 알아보았습니다. 자습서의 다음 단계로 진행하려면

- [자습서: Azure 사용자 지정 공급 기업을 위한 Azure Functions 설정](./tutorial-custom-providers-function-setup.md)

참조 또는 빠른 시작이 필요한 경우 다음과 같은 몇 가지 유용한 링크를 사용할 수 있습니다.

- [빠른 시작: Azure 사용자 지정 공급 기업 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
