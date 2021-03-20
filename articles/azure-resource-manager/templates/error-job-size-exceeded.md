---
title: 작업 크기 초과 오류
description: 작업 크기나 템플릿이 너무 클 때 발생 하는 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610406"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>작업 크기 초과에 대 한 오류 해결

이 문서에서는 **JobSizeExceededException** 및 **DeploymentJobSizeExceededException** 오류를 해결 하는 방법을 설명 합니다.

## <a name="symptom"></a>증상

템플릿을 배포할 때 배포 제한이 초과 되었다는 오류가 표시 됩니다.

## <a name="cause"></a>원인

배포가 허용 되는 한도 중 하나를 초과 하면이 오류가 발생 합니다. 일반적으로이 오류는 배포를 실행 하는 작업 또는 템플릿 중 하나가 너무 클 때 표시 됩니다.

배포 작업은 1mb를 초과할 수 없습니다. 작업에는 요청에 대한 메타데이터가 포함됩니다. 큰 템플릿의 경우 템플릿과 결합된 메타데이터가 작업에 허용되는 크기를 초과할 수 있습니다.


템플릿은 4MB를 초과할 수 없습니다. 4mb 제한은 [복사](copy-resources.md) 를 사용 하 여 여러 인스턴스를 만드는 리소스 정의에 대해 확장 된 후 템플릿의 최종 상태에 적용 됩니다. 최종 상태에는 변수 및 매개 변수에 대해 확인된 값도 포함됩니다.

템플릿에 대 한 다른 한도는 다음과 같습니다.

* 매개 변수 256개
* 변수 256개
* 리소스 800개(인쇄 매수 포함)
* 출력 값 64개
* 템플릿 식의 문자 24,576자

## <a name="solution-1---simplify-template"></a>해결 방법 1-템플릿 단순화

첫 번째 옵션은 템플릿을 간소화 하는 것입니다. 이 옵션은 템플릿에서 다양 한 리소스 형식을 배포 하는 경우에 작동 합니다. 템플릿을 [연결 된 템플릿으로](linked-templates.md)분할 하는 것이 좋습니다. 리소스 종류를 논리 그룹으로 나누고 각 그룹에 대 한 연결 된 템플릿을 추가 합니다. 예를 들어 많은 네트워킹 리소스를 배포 해야 하는 경우 해당 리소스를 연결 된 템플릿으로 이동할 수 있습니다.

연결 된 템플릿에 종속 된 다른 리소스를 설정 하 고 연결 된 [템플릿의 출력에서 값을 가져올](linked-templates.md#get-values-from-linked-template)수 있습니다.

## <a name="solution-2---reduce-name-size"></a>해결 방법 2-이름 크기 줄이기

[매개 변수](template-parameters.md), [변수](template-variables.md)및 [출력](template-outputs.md)에 사용 하는 이름의 길이를 짧게 만들려고 합니다. 이러한 값이 복사 루프를 통해 반복 되 면 많은 이름을 여러 번 곱합니다.

## <a name="solution-3---use-serial-copy"></a>해결 방법 3-직렬 복사본 사용

복사 루프를 [병렬로 변경 하](copy-resources.md#serial-or-parallel)는 것이 좋습니다. 이 옵션은 복사를 통해 많은 수의 리소스를 배포 하 여 오류가 발생 한 것으로 의심 되는 경우에만 사용 합니다. 이러한 변경으로 인해 리소스가 병렬로 배포 되지 않기 때문에 배포 시간이 크게 늘어날 수 있습니다.
