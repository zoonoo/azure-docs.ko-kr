---
title: 보안 코드 모범 사례
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 개발할 때 발생할 수 있는 잠재적인 보안 위협에 대해 알아봅니다. Azure ML이 제공 하는 완화 방법과 개발 환경을 안전 하 게 유지 하기 위한 모범 사례에 대해 알아보세요.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 4bc9a982f6ce77b803a3ba91e050bcda9ec74fed
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728525"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Azure Machine Learning를 사용한 보안 코드 모범 사례

Azure Machine Learning에서 모든 원본의 파일 및 콘텐츠를 Azure로 업로드할 수 있습니다. 로드 하는 Jupyter 노트북 또는 스크립트 내의 콘텐츠는 잠재적으로 세션에서 데이터를 읽거나, Azure에서 조직 내의 데이터에 액세스 하거나, 사용자를 대신해 악의적인 프로세스를 실행할 수 있습니다.

> [!IMPORTANT]
> 신뢰할 수 있는 원본에서 노트북 또는 스크립트만 실행 합니다. 예를 들어 사용자 또는 보안 팀에서 노트북 또는 스크립트를 검토 했습니다.

## <a name="potential-threats"></a>잠재적 위협

Azure Machine Learning를 사용 하 여 개발 하려면 웹 기반 개발 환경 (노트북 & Azure ML studio)이 포함 됩니다. 웹 기반 개발 환경을 사용 하는 경우 잠재적인 위협은 다음과 같습니다.

* [사이트 간 스크립팅 (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __DOM 주입__:이 유형의 공격은 브라우저에 표시 되는 UI를 수정할 수 있습니다. 예를 들어 Jupyter Notebook에서 실행 단추가 동작 하는 방식을 변경 합니다.
    * __액세스 토큰/쿠키__: XSS 공격 에서도 로컬 저장소 및 브라우저 쿠키에 액세스할 수 있습니다. AAD (Azure Active Directory) 인증 토큰은 로컬 저장소에 저장 됩니다. XSS 공격은 사용자 대신 API 호출을 수행 하는 데이 토큰을 사용 하 여 외부 시스템 또는 API로 데이터를 보낼 수 있습니다.

* [CSRF (교차 사이트 요청 위조)](https://owasp.org/www-community/attacks/csrf):이 공격은 이미지의 url 또는 링크를 악성 스크립트나 API의 url로 바꿀 수 있습니다. 이미지를 로드 하거나 링크를 클릭 하면 URL에 대 한 호출이 수행 됩니다.

## <a name="azure-ml-studio-notebooks"></a>Azure ML studio 노트북

Azure Machine Learning studio는 브라우저에서 호스트 된 노트북 환경을 제공 합니다. 노트북의 셀은 악성 코드를 포함 하는 HTML 문서 또는 조각을 출력할 수 있습니다.  출력이 렌더링 되 면 코드를 실행할 수 있습니다.

__가능한 위협__:
* 사이트 간 스크립팅 (XSS)
* CSRF (교차 사이트 요청 위조)

__Azure Machine Learning에서 제공 하는 완화__방법:
* __코드 셀 출력이__ iframe에서 샌드 박싱 됩니다. Iframe은 스크립트가 부모 DOM, 쿠키 또는 세션 저장소에 액세스할 수 없도록 합니다.
* __Markdown 셀__ 콘텐츠는 dompurify 라이브러리를 사용 하 여 정리 됩니다. 이렇게 하면 markdown 셀에서 악성 스크립트가 실행 되지 않도록 차단 됩니다.
* __이미지 URL__ 및 __Markdown 링크__ 는 악성 값을 확인 하는 Microsoft 소유의 끝점으로 전송 됩니다. 악의적인 값이 검색 되 면 끝점이 요청을 거부 합니다.

__권장 작업__:
* Studio로 업로드 하기 전에 파일의 콘텐츠를 신뢰 하는지 확인 합니다. 업로드할 때 신뢰할 수 있는 파일을 업로드 하 고 있음을 인정 해야 합니다.
* 외부 응용 프로그램을 여는 링크를 선택 하면 응용 프로그램을 신뢰 하 라는 메시지가 표시 됩니다.

## <a name="azure-ml-compute-instance"></a>Azure ML 계산 인스턴스

Azure Machine Learning 계산 인스턴스는 __jupyter__ 및 __Jupyter Lab__을 호스팅합니다. 둘 중 하나를 사용 하는 경우 노트북 또는의 코드에 있는 셀은 악성 코드가 포함 된 HTML 문서나 조각을 출력할 수 있습니다. 출력이 렌더링 되 면 코드를 실행할 수 있습니다. 계산 인스턴스에서 호스트 되는 __Rstudio__ 를 사용 하는 경우에도 동일한 위협이 적용 됩니다.

__가능한 위협__:
* 사이트 간 스크립팅 (XSS)
* CSRF (교차 사이트 요청 위조)

__Azure Machine Learning에서 제공 하는 완화__방법:
* 없음 Jupyter 및 Jupyter 랩은 Azure Machine Learning 계산 인스턴스에 호스트 되는 오픈 소스 응용 프로그램입니다.

__권장 작업__:
* Studio로 업로드 하기 전에 파일의 콘텐츠를 신뢰 하는지 확인 합니다. 업로드할 때 신뢰할 수 있는 파일을 업로드 하 고 있음을 인정 해야 합니다.

## <a name="report-security-issues-or-concerns"></a>보안 문제 또는 문제 보고 

Azure Machine Learning은 Microsoft Azure 포상 프로그램에서 사용할 수 있습니다. 자세한 내용은을 참조  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning에 대한 엔터프라이즈 보안](concept-enterprise-security.md)