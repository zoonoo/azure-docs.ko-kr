---
title: Azure에서 사용자 지정 작업 및 리소스 만들기
description: 이 자습서에서는 Azure Resource Manager에서 사용자 지정 작업 및 리소스를 만드는 방법에 대해 설명합니다. 또한 사용자 지정 워크플로가 Azure Resource Manager 템플릿, Azure CLI, Azure Policy 및 Azure 활동 로그와 상호 작용하는 방법을 보여줍니다.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172929"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Azure에서 사용자 지정 작업 및 리소스 만들기

사용자 지정 공급자는 Azure와 엔드포인트 사이의 계약입니다. 사용자 지정 공급자를 사용하면 새 API를 Azure Resource Manager에 추가하여 Azure에서 워크플로를 변경할 수 있습니다. 이러한 사용자 지정 API를 사용하면 Resource Manager에서 새 배포 및 관리 기능을 사용할 수 있습니다.

이 자습서에서는 Azure에 새 작업 및 리소스를 추가하는 방법과 통합하는 방법에 대한 간단한 예제를 살펴봅니다.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure 사용자 지정 공급자를 위한 Azure Functions 설정

이 자습서의 1부에서는 사용자 지정 공급자로 작동하도록 Azure 함수 앱을 설정하는 방법을 보여줍니다.

- [Azure 사용자 지정 공급자를 위한 Azure Functions 설정](./tutorial-custom-providers-function-setup.md)

사용자 지정 공급 기업은 공용 URL을 사용할 수 있습니다.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>사용자 지정 공급자를 위한 RESTful 엔드포인트 작성

이 자습서의 2부에서는 사용자 지정 공급자를 위한 RESTful 엔드포인트를 작성하는 방법을 살펴봅니다.

- [사용자 지정 공급 기업을 위한 RESTful 엔드포인트 작성](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>사용자 지정 공급자 만들기 및 사용

이 자습서의 3부에서는 사용자 지정 공급자를 만들고 해당 사용자 지정 작업 및 리소스를 사용하는 방법을 자세히 알아봅니다.

- [사용자 지정 공급자 만들기 및 사용](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급자가 무엇이며, 어떻게 빌드하는지에 대해 알아보았습니다. 다음 자습서로 계속 진행하려면 [자습서: Azure 사용자 지정 공급자를 위한 Azure Functions 설정](./tutorial-custom-providers-function-setup.md)을 참조하세요.

참조 또는 빠른 시작이 필요한 경우 다음과 같은 몇 가지 유용한 링크를 사용할 수 있습니다.

- [빠른 시작: Azure 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
