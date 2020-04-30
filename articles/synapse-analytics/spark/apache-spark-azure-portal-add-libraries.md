---
title: Azure Synapse Analytics에서 Apache Spark에 대 한 라이브러리 추가 및 관리
description: Azure Synapse Analytics에서 Apache Spark에 사용 되는 라이브러리를 추가 하 고 관리 하는 방법을 알아봅니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870373"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Apache Spark에 대 한 라이브러리 추가 및 관리

Apache Spark는 기능을 제공 하기 위해 많은 라이브러리에 의존 합니다. 이러한 라이브러리는 추가 라이브러리나 이전 버전의 업데이트 된 버전으로 확대 하거나 대체할 수 있습니다.

Python 패키지는 Spark 풀 (미리 보기) 수준에서 추가할 수 있으며 jar 기반 패키지는 Spark 작업 정의 수준에서 추가할 수 있습니다.

## <a name="adding-or-updating-python-libraries"></a>Python 라이브러리 추가 또는 업데이트

Azure Synapse Analytics의 Apache Spark에는 완전 한 Anacondas 설치와 추가 라이브러리가 있습니다. 전체 라이브러리 목록은 [Apache Spark 버전 지원](apache-spark-version-support.md)에서 찾을 수 있습니다.

Spark 인스턴스가 시작 되 면이 설치를 기본으로 사용 하 여 새 가상 환경이 만들어집니다. 또한 *요구 사항 .txt* 파일 ( `pip freeze` 명령의 출력)을 사용 하 여 가상 환경을 업그레이드할 수 있습니다. 설치 또는 업그레이드를 위해이 파일에 나열 된 패키지는 클러스터 시작 시 PyPi에서 다운로드 됩니다. 이 요구 사항 파일은 spark 인스턴스를 Spark 풀에서 만들 때마다 사용 됩니다.

> [!IMPORTANT]
>
> - 설치 하는 패키지가 크거나 설치 하는 데 시간이 오래 걸리는 경우 Spark 인스턴스 시작 시간에 영향을 줍니다.
> - 설치 시 GCC와 같이 컴파일러 지원이 필요한 패키지는 지원 되지 않습니다.
> - 패키지를 다운 그레이드할 수 없습니다. 추가 하거나 업그레이드할 수 있습니다.

### <a name="requirements-format"></a>요구 사항 형식

다음 코드 조각에서는 요구 사항 파일의 형식을 보여 줍니다. PyPi 패키지 이름은 정확한 버전과 함께 나열 됩니다. 이 파일은 [pip 고정](https://pip.pypa.io/en/stable/reference/pip_freeze/) 참조 설명서에 설명 된 형식을 따릅니다. 이 예제에서는 특정 버전을 고정 합니다. 이 파일에서 "보다 큼" 및 "보다 작음" 버전을 지정할 수도 있습니다.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python 라이브러리 사용자 인터페이스

라이브러리를 추가 하는 UI는 Azure Portal에서 **Apache Spark 풀 만들기** 페이지의 **추가 설정** 탭에 있습니다.

페이지의 **패키지** 섹션에서 파일 선택기를 사용 하 여 환경 구성 파일을 업로드 합니다.

![Python 라이브러리 추가](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Python 라이브러리 추가")

### <a name="verifying-installed-libraries"></a>설치 된 라이브러리 확인

올바른 라이브러리의 올바른 버전이 설치 되어 있는지 확인 하려면 다음 코드를 실행 합니다.

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 설명서](https://spark.apache.org/docs/2.4.4/)
