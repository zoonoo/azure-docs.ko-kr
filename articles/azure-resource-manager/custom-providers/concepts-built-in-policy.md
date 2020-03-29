---
title: 정책을 사용하여 연결 배포
description: Azure Policy 서비스를 사용하는 사용자 지정 공급자에 대한 연결 배포에 대해 자세히 알아봅니다.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650475"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Azure 정책을 사용하여 사용자 지정 공급자에 대한 연결 배포

Azure 정책을 사용하여 연결을 배포하여 리소스를 사용자 지정 공급자에 연결할 수 있습니다. 이 문서에서는 연결을 배포하는 기본 제공 정책과 해당 정책을 사용하는 방법에 대해 설명합니다.

## <a name="built-in-policy-to-deploy-associations"></a>연결을 배포하는 기본 제공 정책

사용자 지정 공급자에 대한 배포 연결은 리소스를 사용자 지정 공급자에 연결하는 데 연결을 배포하는 데 사용할 수 있는 기본 제공 정책입니다. 정책은 다음 세 가지 매개 변수를 허용합니다.

- 사용자 지정 공급자 ID - 이 ID는 리소스를 연결해야 하는 사용자 지정 공급자의 리소스 ID입니다.
- 연결할 리소스 유형 - 이러한 리소스 유형은 사용자 지정 공급자에 연결할 리소스 유형 목록입니다. 동일한 정책을 사용하여 여러 리소스 유형을 사용자 지정 공급자에 연결할 수 있습니다.
- 연결 이름 접두사 - 이 문자열은 생성되는 연결 리소스의 이름에 추가할 접두사입니다. 기본값은 "배포된 정책"입니다.

정책은 DeployIfNotExists 평가를 사용합니다. 리소스 공급자가 만들기 또는 업데이트 리소스 요청을 처리하고 평가에서 성공 상태 코드를 반환한 후에 실행됩니다. 그런 다음 연결 리소스는 템플릿 배포를 사용하여 배포됩니다.
연결에 대한 자세한 내용은 [Azure 사용자 지정 공급자 리소스 온보딩을](./concepts-resource-onboarding.md) 참조하십시오.

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>배포 연결 기본 제공 정책을 사용하는 방법 

### <a name="prerequisites"></a>사전 요구 사항
사용자 지정 공급자가 작업을 수행하기 위해 정책 범위에 대한 사용 권한이 필요한 경우 연결 리소스의 정책 배포는 권한을 부여하지 않고 작동하지 않습니다.

### <a name="policy-assignment"></a>정책 할당
기본 제공 정책을 사용하려면 정책 할당을 만들고 사용자 지정 공급자 정책에 대해 Deploy 연결을 할당합니다. 그런 다음 정책은 비준수 리소스를 식별하고 해당 리소스에 대한 연결을 배포합니다.

![기본 제공 정책 할당](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>도움말 보기

Azure 사용자 지정 리소스 공급자 개발에 대 한 질문이 있는 경우 [스택 오버플로에](https://stackoverflow.com/questions/tagged/azure-custom-providers)대 한 질문 을 시도 합니다. 이미 비슷한 질문과 그에 대한 답변이 게시되었을 수도 있으므로, 먼저 확인한 후 게시하세요. 빠른 응답을 얻으려면 ```azure-custom-providers``` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 제공 정책을 사용하여 연결을 배포하는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하십시오.

- [개념: Azure 사용자 지정 공급자 리소스 온보딩](./concepts-resource-onboarding.md)
- [자습서: 사용자 지정 공급자를 사용 하 고 리소스 온보딩](./tutorial-resource-onboarding.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [빠른 시작: 사용자 지정 리소스 공급자를 만들고 사용자 지정 리소스 배포](./create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
