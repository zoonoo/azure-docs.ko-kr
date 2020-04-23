---
title: Azure 시냅스 분석에서 아파치 스파크용 라이브러리 추가 및 관리
description: Azure 시냅스 분석에서 아파치 스파크에서 사용하는 라이브러리를 추가하고 관리하는 방법을 알아봅니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870373"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure 시냅스 분석에서 아파치 스파크용 라이브러리 추가 및 관리

아파치 스파크는 기능을 제공하기 위해 많은 라이브러리에 따라 달라집니다. 이러한 라이브러리는 추가 라이브러리 또는 업데이트된 버전의 이전 라이브러리로 보강하거나 대체할 수 있습니다.

파이썬 패키지는 스파크 풀(미리 보기) 수준에서 추가할 수 있으며 .jar 기반 패키지는 Spark 작업 정의 수준에서 추가할 수 있습니다.

## <a name="adding-or-updating-python-libraries"></a>파이썬 라이브러리 추가 또는 업데이트

Azure 시냅스 애널리틱스의 아파치 스파크에는 아나콘다 설치와 추가 라이브러리가 있습니다. 전체 라이브러리 목록은 [아파치 스파크 버전 지원에서](apache-spark-version-support.md)찾을 수 있습니다.

Spark 인스턴스가 시작되면 이 설치를 기반으로 새 가상 환경이 만들어집니다. 또한 *요구 사항.txt* 파일(명령에서 `pip freeze` 출력)을 사용하여 가상 환경을 업그레이드할 수 있습니다. 설치 또는 업그레이드를 위해 이 파일에 나열된 패키지는 클러스터 시작 시 PyPi에서 다운로드됩니다. 이 요구 사항 파일은 해당 Spark 풀에서 Spark 인스턴스를 만들 때마다 사용됩니다.

> [!IMPORTANT]
>
> - 설치하는 패키지가 크거나 설치하는 데 시간이 오래 걸리는 경우 이는 Spark 인스턴스 시작 시간에 영향을 줍니다.
> - 설치 시 컴파일러 지원이 필요한 패키지(예: GCC)는 지원되지 않습니다.
> - 패키지를 다운그레이드하거나 추가하거나 업그레이드할 수 없습니다.

### <a name="requirements-format"></a>요구 사항 형식

다음 코드 조각에는 요구 사항 파일의 형식이 표시됩니다. PyPi 패키지 이름은 정확한 버전과 함께 나열됩니다. 이 파일은 [pip 동결](https://pip.pypa.io/en/stable/reference/pip_freeze/) 참조 설명서에 설명된 형식을 따릅니다. 이 예제는 특정 버전을 고정합니다. 이 파일에서 "보다 크지 않은" 및 "보다 작다"버전을 지정할 수도 있습니다.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>파이썬 라이브러리 사용자 인터페이스

라이브러리를 추가하기 위한 UI는 Azure 포털의 **아파치 스파크 풀 만들기** 페이지의 **추가 설정** 탭에 있습니다.

페이지의 패키지 섹션에서 파일 선택기에서 환경 구성 파일을 **업로드합니다.**

![파이썬 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "파이썬 라이브러리 추가")

### <a name="verifying-installed-libraries"></a>설치된 라이브러리 확인

올바른 라이브러리의 올바른 버전이 설치되어 있는지 확인하려면 다음 코드를 실행합니다.

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 설명서](https://spark.apache.org/docs/2.4.4/)
