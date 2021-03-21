---
title: Azure Data Factory 엔터티 명명 규칙
description: 데이터 팩터리 엔터티에 대한 이름 지정 규칙을 설명합니다.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: bef8706d9dcef966397dcddc64ffbd567a8431f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034407"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 이름 지정 규칙

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

다음 테이블에서 데이터 팩터리 아티팩트에 대한 이름 지정 규칙을 제공합니다.

| Name | 이름 고유성 | 유효성 검사 |
|:--- |:--- |:--- |
| Data Factory | Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. 즉, `MyDF` 및 `mydf`는 같은 데이터 팩터리를 의미합니다. |<ul><li>각 데이터 팩터리는 정확히 하나의 Azure 구독에 연결됩니다.</li><li>개체 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.</li><li>모든 대시(-) 문자는 앞뒤에 문자 또는 숫자가 와야 합니다. 컨테이너 이름에서 연속 대시는 허용되지 않습니다.</li><li>이름은 3-63자까지 가능합니다.</li></ul> |
| 연결 된 서비스/데이터 집합/파이프라인/데이터 흐름 | 데이터 팩터리 내에서 고유 합니다. 이름은 대/소문자를 구분하지 않습니다. |<ul><li>개체 이름은 문자로 시작 해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li><li>대시 ("-")는 연결 된 서비스, 데이터 흐름 및 데이터 집합의 이름에 사용할 수 없습니다.</li></ul>  |
| 통합 런타임 |데이터 팩터리 내에서 고유 합니다. 이름은 대/소문자를 구분하지 않습니다. |<ul><li>통합 런타임 이름에는 문자, 숫자 및 대시 (-) 문자만 사용할 수 있습니다.</li><li>첫 번째 및 마지막 문자는 문자 또는 숫자 여야 합니다. 모든 대시(-) 문자는 앞뒤에 문자 또는 숫자가 와야 합니다.</li><li>통합 런타임 이름에는 연속 대시를 사용할 수 없습니다. </li></ul> |
| 데이터 흐름 변환 | 데이터 흐름 내에서 고유 합니다. 이름은 대/소문자를 구분 하지 않습니다. | <ul><li>데이터 흐름 변환 이름에는 문자와 숫자만 사용할 수 있습니다.</li><li>첫 글자는 문자여야 합니다. </li></ul> |
| 리소스 그룹 |Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. | 자세한 내용은 [Azure 명명 규칙 및 제한 사항](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)을 참조하세요. |
| 파이프라인 매개 변수 & 변수  |파이프라인 내에서 고유 합니다. 이름은 대/소문자를 구분하지 않습니다. | <ul><li>이전 버전과의 호환성 때문에 매개 변수 이름 및 변수 이름에 대 한 유효성 검사는 고유성으로 제한 됩니다.</li><li>매개 변수 또는 변수를 사용 하 여 엔터티 이름 (예: 연결 된 서비스)을 참조할 때 엔터티 명명 규칙이 적용 됩니다.</li><li>파이프라인 매개 변수 및 변수의 이름을 지정 하기 위해 데이터 흐름 변환 명명 규칙을 따르는 것이 좋습니다.</li></ul> |

## <a name="next-steps"></a>다음 단계

[빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md) 문서의 단계별 지침에 따라 데이터 팩터리를 만드는 방법에 대해 알아봅니다. 
