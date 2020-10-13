---
author: baanders
description: Azure Digital Twins 모델의 유효성을 검사 하는 방법을 설명 하는 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: da42f847bdd57c4dae0cde673c9c9e08d51a758d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998307"
---
> [!TIP]
> 모델을 만든 후에는 Azure Digital Twins 인스턴스에 업로드 하기 전에 오프 라인으로 모델의 유효성을 검사 하는 것이 좋습니다.

모델 문서의 유효성을 검사 하 여 DTDL이 올바른지 확인 하는 데 사용할 수 있는 언어를 제한 하지 않는 샘플을 인스턴스에 업로드 합니다. [**Dtdl Validator 샘플**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)에서 찾을 수 있습니다.

* DTDL 유효성 검사기 샘플은 클라이언트 쪽 라이브러리 ( [**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/))로 NuGet에서 사용할 수 있는 .NET dtdl 파서 라이브러리를 기반으로 합니다. 라이브러리를 직접 사용 하 여 고유한 유효성 검사 솔루션을 디자인할 수도 있습니다. 파서 라이브러리를 사용 하는 경우 Azure 디지털 Twins에서 실행 중인 버전과 호환 되는 버전을 사용 해야 합니다. 미리 보기 중에는 버전 *3.12.4*입니다.

사용 예제를 비롯 한 유효성 검사기 샘플 및 파서 라이브러리에 대 한 자세한 내용은 [*방법: 모델 구문 분석 및 유효성 검사*](../articles/digital-twins/how-to-parse-models.md)를 통해 확인할 수 있습니다.