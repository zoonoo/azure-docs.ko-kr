---
title: 정책을 사용 하 여 관리 되는 응용 프로그램에 대 한 연결 배포
description: Azure Policy 서비스를 사용 하 여 관리 되는 응용 프로그램에 대 한 연결 배포에 대해 알아봅니다.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650943"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Azure Policy를 사용 하 여 관리 되는 응용 프로그램에 대 한 연결 배포

Azure 정책을 사용 하 여 관리 되는 응용 프로그램에 리소스를 연결 하는 연결을 배포할 수 있습니다. 이 문서에서는 연결을 배포 하는 기본 제공 정책과 해당 정책을 사용 하는 방법을 설명 합니다.

## <a name="built-in-policy-to-deploy-associations"></a>연결을 배포 하기 위한 기본 제공 정책

관리 되는 응용 프로그램에 대 한 연결 배포는 연결을 배포 하 여 관리 되는 응용 프로그램에 리소스를 연결 하는 데 사용할 수 있는 기본 제공 정책입니다. 이 정책은 다음 세 개의 매개 변수를 허용 합니다.

- 관리 되는 응용 프로그램 ID-이 ID는 리소스를 연결 해야 하는 관리 되는 응용 프로그램의 리소스 ID입니다.
- 연결할 리소스 종류-이러한 리소스 형식은 관리 되는 응용 프로그램에 연결 되는 리소스 종류 목록입니다. 동일한 정책을 사용 하 여 여러 리소스 종류를 관리 되는 응용 프로그램에 연결할 수 있습니다.
- 연결 이름 접두사-이 문자열은 생성 되는 연결 리소스의 이름에 추가 되는 접두사입니다. 기본값은 "DeployedByPolicy"입니다.

정책에서 DeployIfNotExists 평가를 사용 합니다. 리소스 공급자가 선택한 리소스 종류의 리소스 만들기 또는 업데이트 요청을 처리 하 고 평가가 성공 상태 코드를 반환한 후에 실행 됩니다. 그런 다음 템플릿 배포를 사용 하 여 연결 리소스를 배포 합니다.
연결에 대 한 자세한 내용은 [Azure 사용자 지정 공급자 리소스 온 보 딩](../custom-providers/concepts-resource-onboarding.md) 을 참조 하세요.

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>연결 배포 기본 제공 정책을 사용 하는 방법 

### <a name="prerequisites"></a>필수 조건
관리 되는 응용 프로그램에서 작업을 수행 하기 위해 구독에 대 한 권한이 필요한 경우 사용 권한을 부여 하지 않으면 연결 리소스의 정책 배포가 작동 하지 않습니다.

### <a name="policy-assignment"></a>정책 할당
기본 제공 정책을 사용 하려면 정책 할당을 만들고 관리 되는 응용 프로그램 정책에 대 한 연결 배포를 할당 합니다. 정책이 성공적으로 할당 되 면 정책을 준수 하지 않는 리소스를 식별 하 고 해당 리소스에 대 한 연결을 배포 합니다.

![기본 제공 정책 할당](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>도움말 보기

Azure 사용자 지정 리소스 공급자 개발에 대 한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)에 요청 해 보세요. 이미 비슷한 질문과 그에 대한 답변이 게시되었을 수도 있으므로, 먼저 확인한 후 게시하세요. 빠른 응답을 얻으려면 ```azure-custom-providers``` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 제공 정책을 사용 하 여 연결을 배포 하는 방법을 학습. 자세히 알아보려면 다음 문서를 참조 하세요.

- [개념: Azure 사용자 지정 공급자 리소스 온 보 딩](../custom-providers/concepts-resource-onboarding.md)
- [자습서: 사용자 지정 공급자를 사용 하 여 리소스 온 보 딩](../custom-providers/tutorial-resource-onboarding.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [빠른 시작: 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](../custom-providers/create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](../custom-providers/custom-providers-resources-endpoint-how-to.md)
