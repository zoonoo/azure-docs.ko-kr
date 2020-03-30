---
title: 리소스 이름 지정 제한
description: Azure 리소스 의 이름 지정에 대한 규칙 및 제한 사항을 보여 주며
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 580b7efd26d5729baf236b59452f63483c003e44
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366213"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 리소스에 대한 명명 규칙 및 제한 사항

이 문서에서는 Azure 리소스에 대한 명명 규칙 및 제한 사항을 요약합니다. 리소스 이름을 지정하는 방법에 대한 권장 사항은 [준비: 권장 이름 지정 및 태그 지정 규칙을](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)참조하십시오.

리소스 이름은 유효한 문자 열에 특별히 명시되지 않는 한 대/소문자를 구분하지 않습니다.

다음 표에서 용어 는 "숫자"를 참조합니다.

* **a** through z(소문자) **z**
* **A~** Z(대문자) **Z**
* **0부터** 9까지(숫자) **9**

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 리소스 그룹 | 3-63 | 소문자와 숫자.<br><br>소문자로 시작합니다. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 서비스 | 전역 | 1-50 | 숫자.<br><br>문자로 시작합니다. |
> | 서비스 / APIS | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / APIS / 문제 | api | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / APIS / 문제 / 첨부 파일 | 문제 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / APIS / 문제 / 코멘트 | 문제 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / APIS / 운영 | api | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / APIS / 작업 / 태그 | operation(작업) | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / APIS / 릴리스 | api | 1-80 | 숫자, 밑줄 및 하이픈입니다.<br><br>시작 및 끝은 숫자 또는 밑줄. |
> | 서비스 / APIS / 스키마 | api | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / apis / 태그 설명 | api | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / APIS / 태그 | api | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / API 버전 세트 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 권한 부여서버 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 백엔드 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 인증서 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 진단 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 그룹 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 그룹 / 사용자 | group | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / ID공급자 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 로거 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 알림 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 알림 / 수신자이메일 | 알림 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 오픈 리드커넥스제공자 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 정책 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 제품 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 제품 / APIS | product | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 제품 / 그룹 | product | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 제품 / 태그 | product | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 속성 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 구독 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 태그 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 템플릿 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |
> | 서비스 / 사용자 | 서비스 | 1-256 | 사용할 수 없습니다.<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>마이크로소프트.앱구성

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 구성 스토어 | 리소스 그룹 | 5-50 | 숫자, 밑줄 및 하이픈입니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | locks | 할당 범위 | 1-90 | 숫자, 마침표, 밑줄, 하이픈 및 괄호.<br><br>마침표로 끝날 수 없습니다. |
> | policyassignments | 할당 범위 | 1-128 디스플레이 이름<br><br>1-260 리소스 이름 | 표시 이름에는 모든 문자가 포함될 수 있습니다.<br><br>리소스 이름은 포함할 `%` 수 없으며 기간이나 공간으로 끝낼 수 없습니다. |
> | 정책 정의 | 정의 범위 | 1-128 디스플레이 이름<br><br>1-260 리소스 이름 | 표시 이름에는 모든 문자가 포함될 수 있습니다.<br><br>리소스 이름은 포함할 `%` 수 없으며 기간이나 공간으로 끝낼 수 없습니다. |
> | policySetDefinitions | 정의 범위 | 1-128 디스플레이 이름<br><br>1-260 리소스 이름 | 표시 이름에는 모든 문자가 포함될 수 있습니다.<br><br>리소스 이름은 포함할 `%` 수 없으며 기간이나 공간으로 끝낼 수 없습니다.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | automationAccounts | 리소스 그룹 | 6-50 | 숫자와 하이픈.<br><br>문자로 시작하고 숫자로 끝납니다. |
> | 자동화계정 / 인증서 | 자동화 계정 | 1-128 | 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다.  |
> | 자동화계정 / 연결 | 자동화 계정 | 1-128 | 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | 자동화계정/ 자격 증명 | 자동화 계정 | 1-128 | 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | 자동화계정 / 런북 | 자동화 계정 | 1-63 | 숫자, 밑줄 및 하이픈입니다.<br><br>문자로 시작합니다.  |
> | 자동화계정/ 일정 | 자동화 계정 | 1-128 | 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | 자동화계정/변수 | 자동화 계정 | 1-128 | 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |
> | 자동화계정 / 감시자 | 자동화 계정 | 1-63 |  숫자, 밑줄 및 하이픈입니다.<br><br>문자로 시작합니다. |
> | 자동화계정 / 웹후크 | 자동화 계정 | 1-128 | 사용할 수 없습니다.<br> `<>*%&:\?.+/` <br><br>공백으로 끝날 수 없습니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | batchAccounts | 지역 | 3-24 | 소문자와 숫자. |
> | 일괄 계정 / 응용 프로그램 | 일괄 처리 계정 | 1-64 | 숫자, 밑줄 및 하이픈입니다. |
> | 일괄 계정 / 인증서 | 일괄 처리 계정 | 5-45 | 숫자, 밑줄 및 하이픈입니다. |
> | 일괄 계정 / 풀 | 일괄 처리 계정 | 1-64 | 숫자, 밑줄 및 하이픈입니다. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 블록체인 회원 | 전역 | 2-20 | 소문자와 숫자.<br><br>소문자로 시작합니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | botServices | 전역 | 2-64 |  숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. |
> | 봇 서비스 / 채널 | 봇 서비스 | 2-64 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. |
> | 봇 서비스 / 연결 | 봇 서비스 | 2-64 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. |
> | 엔터프라이즈채널 | 리소스 그룹 | 2-64 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | Redis | 전역 | 1-63 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. 연속 하이픈은 허용되지 않습니다. |
> | 레디스 / 방화벽규칙 | Redis | 1-256 | 숫자 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 프로필 | 리소스 그룹 | 1-260 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |
> | 프로파일 / 끝점 | 전역 | 1-50 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | certificateOrders | 리소스 그룹 | 3-30 | 숫자. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 리소스 그룹 | 2-64 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | availabilitySets | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 숫자 또는 밑줄로 끝입니다. |
> | 디스크 암호화 세트 | 리소스 그룹 | 1-80 | 숫자와 밑줄. |
> | disks | 리소스 그룹 | 1-80 | 숫자와 밑줄. |
> | galleries | 리소스 그룹 | 1-80 | 숫자 및 기간.<br><br>시작 및 끝은 숫자로. |
> | 갤러리 / 응용 프로그램 | 갤러리 | 1-80 | 숫자, 하이픈 및 마침표.<br><br>시작 및 끝은 숫자로. |
> | 갤러리 / 응용 프로그램 / 버전 | 애플리케이션 | 32비트 정수 | 숫자 와 기간. |
> | 갤러리 / 이미지 | 갤러리 | 1-80 | 숫자, 하이픈 및 마침표.<br><br>시작 및 끝은 숫자로. |
> | 갤러리 / 이미지 / 버전 | 이미지 | 32비트 정수 | 숫자 와 기간. |
> | images | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 숫자 또는 밑줄로 끝입니다. |
> | 스냅샷 | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 숫자 또는 밑줄로 끝입니다. |
> | virtualMachines | 리소스 그룹 | 1-15 (윈도우)<br>1-64 (리눅스)<br><br>다음 참고를 참조하세요. | 사용할 수 없습니다.<br> `\/""[]:|<>+=;,?*@&`<br><br>밑줄로 시작할 수 없습니다. 마침표 나 하이픈으로 끝낼 수 없습니다. |
> | virtualMachineScaleSets | 리소스 그룹 | 1-15 (윈도우)<br>1-64 (리눅스)<br><br>다음 참고를 참조하세요. | 사용할 수 없습니다.<br> `\/""[]:|<>+=;,?*@&`<br><br>밑줄로 시작할 수 없습니다. 마침표 나 하이픈으로 끝낼 수 없습니다. |

> [!NOTE]
> Azure 가상 시스템에는 리소스 이름과 호스트 이름이라는 두 가지 이름이 있습니다. 포털에서 가상 컴퓨터를 만들 때 두 이름에 동일한 값이 사용됩니다. 앞의 표의 제한 사항은 호스트 이름에 대한 제한입니다. 실제 리소스 이름은 최대 64자까지 가능합니다.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | containerGroups | 리소스 그룹 | 1-63 | 소문자, 숫자 및 하이픈.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. 연속 하이픈은 허용되지 않습니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | registries | 전역 | 5-50 | 숫자. |
> | 레지스트리 / 빌드작업 | 사용된 | 5-50 | 숫자. |
> | 레지스트리 / 빌드작업/단계 | 빌드 작업 | 5-50 | 숫자. |
> | 레지스트리 / 복제 | 사용된 | 5-50 | 숫자. |
> | 레지스트리 / 범위맵 | 사용된 | 5-50 | 숫자, 하이픈 및 밑줄. |
> | 레지스트리 / 작업 | 사용된 | 5-50 | 숫자, 하이픈 및 밑줄. |
> | 레지스트리 / 토큰 | 사용된 | 5-50 | 숫자, 하이픈 및 밑줄. |
> | 레지스트리 / 웹 후크 | 사용된 | 5-50 | 숫자. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managedClusters | 리소스 그룹 | 1-63 | 숫자, 밑줄 및 하이픈입니다.<br><br>시작 및 끝은 숫자로. |
> | 오픈 시프트관리 클러스터 | 리소스 그룹 | 1-30 | 숫자. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | hubs | 리소스 그룹 | 1-64 | 숫자.<br><br>문자로 시작합니다.  |
> | 허브 / 권한 부여정책 | 허브 | 1-50 | 숫자, 밑줄 및 마침표입니다.<br><br>시작 및 끝은 숫자로. |
> | 허브 / 커넥터 | 허브 | 1-128 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 커넥터 / 매핑 | 커넥터 | 1-128 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 상호 작용 | 허브 | 1-128 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / kpi | 허브 | 1-512 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 링크 | 허브 | 1-512 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 예측 | 허브 | 1-512 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 프로필 | 허브 | 1-128 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 관계링크 | 허브 | 1-512 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 관계 | 허브 | 1-512 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 역할할당 | 허브 | 1-128 | 숫자와 밑줄.<br><br>문자로 시작합니다. |
> | 허브 / 보기 | 허브 | 1-512 | 숫자와 밑줄.<br><br>문자로 시작합니다. |

## <a name="microsoftcustomproviders"></a>마이크로소프트.사용자 지정 공급자

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 연결 | 리소스 그룹 | 1-180 | 사용할 수 없습니다.<br>`%&\\?/`<br><br>마침표 나 공간으로 끝낼 수 없습니다. |
> | 리소스 공급자 | 리소스 그룹 | 3-64 | 사용할 수 없습니다.<br>`%&\\?/`<br><br>마침표 나 공간으로 끝낼 수 없습니다. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | jobs | 리소스 그룹 | 3-24 | 숫자, 하이픈, 밑줄 및 마침표. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | workspaces | 리소스 그룹 | 3-30 | 숫자, 밑줄 및 하이픈 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | factories | 전역 | 3-63 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |
> | 공장 / 데이터 흐름 | 공장 | 1-260 | 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>부터 시작 하십시오. |
> | 공장 / 데이터 세트 | 공장 | 1-260 | 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>부터 시작 하십시오. |
> | 공장 / 통합실행시간 | 공장 | 3-63 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |
> | 공장 / 링크 된 서비스 | 공장 | 1-260 | 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>부터 시작 하십시오. |
> | 공장 / 파이프 라인 | 공장 | 1-260 | 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>부터 시작 하십시오. |
> | 공장 / 트리거 | 공장 | 1-260 | 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>부터 시작 하십시오. |
> | 공장 / 트리거 / 재실행트리거 | 트리거 | 1-260 | 사용할 수 없습니다.<br>`<>*#.%&:\\+?/`<br><br>부터 시작 하십시오. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 전역 | 3-24 | 소문자와 숫자. |
> | 계정 / 계산정책 | account | 3-60 | 숫자, 하이픈 및 밑줄. |
> | 계정 / 데이터레이크스토어계정 | account | 3-24 | 소문자와 숫자. |
> | 계정 / 방화벽규칙 | account | 3-50 | 숫자, 하이픈 및 밑줄. |
> | 계정 / 저장소 계정 계정 | account | 3-60 | 숫자, 하이픈 및 밑줄. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 전역 | 3-24 | 소문자와 숫자. |
> | 계정 / 방화벽규칙 | account | 3-50 | 숫자, 하이픈 및 밑줄. |
> | 계정 / 가상 네트워크규칙 | account | 3-50 | 숫자, 하이픈 및 밑줄. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | services | 리소스 그룹 | 2-62 | 숫자, 하이픈, 마침표 및 밑줄.<br><br>부터 시작 하십시오. |
> | 서비스 / 프로젝트 | 서비스 | 2-57 | 숫자, 하이픈, 마침표 및 밑줄.<br><br>부터 시작 하십시오. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 전역 | 3-63 | 소문자, 하이픈 및 숫자.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. |
> | 서버 / 데이터베이스 | servers | 1-63 | 숫자와 하이픈. |
> | 서버 / 방화벽규칙 | servers | 1-128 | 숫자, 하이픈 및 밑줄. |
> | 서버 / 가상 네트워크규칙 | servers | 1-128 | 숫자와 하이픈. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 전역 | 3-63 | 소문자, 하이픈 및 숫자.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. |
> | 서버 / 데이터베이스 | servers | 1-63 | 숫자와 하이픈. |
> | 서버 / 방화벽규칙 | servers | 1-128 | 숫자, 하이픈 및 밑줄. |
> | 서버 / 가상 네트워크규칙 | servers | 1-128 | 숫자와 하이픈. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | servers | 전역 | 3-63 | 소문자, 하이픈 및 숫자.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. |
> | 서버 / 데이터베이스 | servers | 1-63 | 숫자와 하이픈. |
> | 서버 / 방화벽규칙 | servers | 1-128 | 숫자, 하이픈 및 밑줄. |
> | 서버 / 가상 네트워크규칙 | servers | 1-128 | 숫자와 하이픈. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | IotHubs | 전역 | 3-50 | 숫자와 하이픈.<br><br>하이픈으로 끝낼 수 없습니다. |
> | IotHubs / 인증서 | IoT 허브 | 1-64 | 숫자, 하이픈, 마침표 및 밑줄. |
> | IotHubs / 이벤트허브엔드포인트 / 소비자그룹 | 이벤트허브엔드포인트 | 1-50 | 숫자, 하이픈, 마침표 및 밑줄. |
> | 프로비저닝서비스 | 리소스 그룹 | 3-64 | 숫자와 하이픈.<br><br>숫자로 끝납니다. |
> | 프로비저닝서비스/인증서 | 프로비저닝서비스 | 1-64 | 숫자, 하이픈, 마침표 및 밑줄. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | labs | 리소스 그룹 | 1-50 | 숫자, 밑줄 및 하이픈입니다. |
> | 랩 / 사용자 정의 이미지 | 랩 | 1-80 | 숫자, 밑줄, 하이픈 및 괄호. |
> | 실험실 / 수식 | 랩 | 1-80 | 숫자, 밑줄, 하이픈 및 괄호. |
> | 랩 / 가상 머신 | 랩 | 1-15 (윈도우)<br>1-64 (리눅스) | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. 모든 숫자가 될 수 없습니다. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | databaseAccounts | 전역 | 3-31 | 소문자, 숫자 및 하이픈.<br><br>소문자 또는 숫자로 시작합니다. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | domains | 리소스 그룹 | 3-50 | 숫자와 하이픈. |
> | 도메인 / 주제 | 도메인 | 3-50 | 숫자와 하이픈. |
> | eventSubscriptions | 리소스 그룹 | 3-64 | 숫자와 하이픈. |
> | topics | 리소스 그룹 | 3-50 | 숫자와 하이픈. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 리소스 그룹 | 6-50 | 숫자와 하이픈.<br><br>문자로 시작합니다. 문자 또는 번호로 끝납니다. |
> | 네임스페이스 | 전역 | 6-50 | 숫자와 하이픈.<br><br>문자로 시작합니다. 문자 또는 번호로 끝납니다. |
> | 네임스페이스 / 권한 부여규칙 | namespace | 1-50 | 숫자, 마침표, 하이픈 및 밑줄.<br><br>문자 또는 번호로 시작하고 끝납니다. |
> | 네임스페이스 / 재해복구구성 | namespace | 1-50 | 숫자, 마침표, 하이픈 및 밑줄.<br><br>문자 또는 번호로 시작하고 끝납니다. |
> | 네임스페이스/ 이벤트 허브 | namespace | 1-50 | 숫자, 마침표, 하이픈 및 밑줄.<br><br>문자 또는 번호로 시작하고 끝납니다. |
> | 네임스페이스 / 이벤트 허브 / 권한 부여규칙 | 이벤트 허브 | 1-50 | 숫자, 마침표, 하이픈 및 밑줄.<br><br>문자 또는 번호로 시작하고 끝납니다. |
> | 네임스페이스 / 이벤트 허브 / 소비자 그룹 | 이벤트 허브 | 1-50 | 숫자, 마침표, 하이픈 및 밑줄.<br><br>문자 또는 번호로 시작하고 끝납니다. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 전역 | 3-59 | 숫자 및 하이픈<br><br>문자 또는 번호로 시작하고 끝납니다. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | jobs | 리소스 그룹 | 2-64 | 숫자와 하이픈.<br><br>문자로 시작합니다. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | components | 리소스 그룹 | 1-260 | 사용할 수 없습니다.<br>`%&\?/` <br><br>공간이나 마침표로 끝날 수 없습니다.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | IoTApps | 전역 | 2-63 | 소문자, 숫자 및 하이픈.<br><br>소문자 또는 숫자로 시작합니다. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | vaults | 전역 | 3-24 | 숫자와 하이픈.<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다. 연속된 하이픈을 포함할 수 없습니다. |
> | 볼트 / 비밀 | Vault | 1-127 | 숫자와 하이픈. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 전역 | 4-22 | 소문자와 숫자.<br><br>문자로 시작합니다. |
> | /클러스터/데이터베이스 | cluster | 1-260 | 영숫자, 하이픈, 공백 및 마침표. |
> | /클러스터/ 데이터베이스 / 데이터연결 | 데이터베이스 | 1-40 | 영숫자, 하이픈, 공백 및 마침표. |
> | /클러스터/ 데이터베이스 / 이벤트 허브 연결 | 데이터베이스 | 1-40 | 영숫자, 하이픈, 공백 및 마침표. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | integrationAccounts | 리소스 그룹 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합계정/어셈블리 | 통합 계정 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합계정 / 일괄 구성 | 통합 계정 | 1-20 | 숫자. |
> | 통합계정/증명서 | 통합 계정 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합계정 / 지도 | 통합 계정 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합계정/파트너 | 통합 계정 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합계정 /로제타넷프로세스구성 | 통합 계정 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합계정/스키마 | 통합 계정 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합계정/세션 | 통합 계정 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |
> | 통합서비스환경 | 리소스 그룹 | 1-80 | 숫자, 하이픈, 마침표 및 밑줄. |
> | 통합서비스환경 / 관리형 | 통합 서비스 환경 | 1-80 | 숫자, 하이픈, 마침표 및 밑줄. |
> | workflows | 리소스 그룹 | 1-80 | 숫자, 하이픈, 밑줄, 마침표 및 괄호. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | commitmentPlans | 리소스 그룹 | 1-260 | 사용할 수 없습니다.<br>`<>*%&:?+/\\`<br><br>공백으로 끝낼 수 없습니다. |
> | webServices | 리소스 그룹 | 1-260 | 사용할 수 없습니다.<br>`<>*%&:?+/\\`<br><br>공백으로 끝낼 수 없습니다. |
> | workspaces | 리소스 그룹 | 1-260 | 사용할 수 없습니다.<br>`<>*%&:?+/\\`<br><br>공백으로 끝낼 수 없습니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | workspaces | 리소스 그룹 | 3-33 | 숫자와 하이픈. |
> | 작업 영역/ 계산 | 작업 영역 | 2-16 | 숫자와 하이픈. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 리소스 그룹 | 3-128 | 숫자, 하이픈 및 밑줄<br><br>문자 또는 번호로 시작합니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 계정 | 리소스 그룹 | 1-98(리소스 그룹 이름 및 계정 이름) | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | mediaservices | 리소스 그룹 | 3-24 | 소문자와 숫자. |
> | 미디어 서비스 / 라이브 이벤트 | 미디어 서비스 | 1-32 | 숫자와 하이픈.<br><br>부터 시작 하십시오. |
> | 미디어 서비스 / 라이브 이벤트 / 라이브 출력 | 라이브 이벤트 | 1-256 | 숫자와 하이픈.<br><br>부터 시작 하십시오. |
> | 미디어 서비스 / 스트리밍엔드포인트 | 미디어 서비스 | 1-24 | 숫자와 하이픈.<br><br>부터 시작 하십시오. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | applicationGateways | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | applicationSecurityGroups | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | azureFirewalls | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 숫자 또는 밑줄로 끝입니다. |
> | 요새호스트 | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | connections | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | dnsZones | 리소스 그룹 | 1-63자<br><br>2 ~ 34개의 레이블<br><br>각 레이블은 마침표로 구분된 문자 집합입니다. 예를 들어 **contoso.com** 2개의 레이블이 있습니다. | 각 레이블에는 참숫자, 밑줄 및 하이픈이 포함될 수 있습니다.<br><br>각 레이블은 마침표로 구분됩니다. |
> | expressRouteCircuits | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 방화벽정책 | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 방화벽정책/규칙그룹 | 방화벽 정책 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 프론트 도어 | 전역 | 5-64 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |
> | loadBalancers | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 로드 밸런스 / 인바운드내나트규칙 | 부하 분산 장치 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | localNetworkGateways | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | networkInterfaces | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | networkSecurityGroups | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 네트워크보안그룹 / 보안규칙 | 네트워크 보안 그룹 | 1-80 |  숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | networkWatchers | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 프라이빗DnsZones | 리소스 그룹 | 1-63자<br><br>2 ~ 34개의 레이블<br><br>각 레이블은 마침표로 구분된 문자 집합입니다. 예를 들어 **contoso.com** 2개의 레이블이 있습니다. | 각 레이블에는 참숫자, 밑줄 및 하이픈이 포함될 수 있습니다.<br><br>각 레이블은 마침표로 구분됩니다. |
> | 프라이빗DnsZones / 가상네트워크링크 | 프라이빗 DNS 존 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | publicIPAddresses | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | publicIPPrefixes | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | routeFilters | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 경로 필터 / 경로필터규칙 | 경로 필터 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | routeTables | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 경로테이블 / 경로 | 경로 테이블 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | serviceEndpointPolicies | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | trafficmanagerprofiles | 전역 | 1-63 | 숫자, 하이픈 및 마침표.<br><br>시작 및 끝은 숫자로. |
> | virtualNetworkGateways | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | virtualNetworks | 리소스 그룹 | 2-64 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 가상 네트워크 / 서브넷 | 가상 네트워크 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | 가상 네트워크 / 가상 네트워크 피어링 | 가상 네트워크 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | virtualWans | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | vpnGateways | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | vpn게이트웨이 / VPN연결 | VPN 게이트웨이 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |
> | vpnSites | 리소스 그룹 | 1-80 | 숫자, 밑줄, 마침표 및 하이픈.<br><br>부터 시작 하십시오. 끝 은 숫자 또는 밑줄. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 전역 | 6-50 | 숫자 및 하이픈<br><br>시작 및 끝은 숫자로. |
> | 네임스페이스 / 권한 부여규칙 | namespace | 1-256 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>숫자로 시작합니다. |
> | 네임스페이스 / 알림허브 | namespace | 1-260 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>숫자로 시작합니다. |
> | 네임스페이스 / 알림허브 / 권한 부여규칙 | 알림 허브 | 1-256 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>숫자로 시작합니다. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | 리소스 그룹 | 4-63 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |
> | workspaces | 리소스 그룹 | 4-63 | 숫자와 하이픈.<br><br>시작 및 끝은 숫자로. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | dashboards | 리소스 그룹 | 3-160 | 숫자와 하이픈.<br><br>제한된 문자를 사용하려면 사용할 대시보드 이름으로 **숨겨진 제목이라는** 태그를 추가합니다. 포털은 대시보드를 표시할 때 해당 이름을 표시합니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 숫자와 하이픈.<br><br>하이픈으로 시작할 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | 소문자 또는 숫자<br><br>소문자로 시작합니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | vaults | 리소스 그룹 | 2-50 | 숫자와 하이픈.<br><br>문자로 시작합니다. |
> | 볼트 / 백업정책 | 자격 증명 모음 | 3-150 | 숫자와 하이픈.<br><br>문자로 시작합니다. 하이픈으로 끝낼 수 없습니다. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 전역 | 6-50 | 숫자와 하이픈.<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다. |
> | 네임스페이스 / 권한 부여규칙 | namespace | 1-50 |  숫자, 마침표, 하이픈 및 밑줄.<br><br>시작 및 끝은 숫자로. |
> | 네임스페이스 / 하이브리드 연결 | namespace | 1-260 | 숫자, 마침표, 하이픈, 밑줄 및 슬래시.<br><br>시작 및 끝은 숫자로. |
> | 네임스페이스 / 하이브리드 연결/권한 부여규칙 | 하이브리드 연결 | 1-50 | 숫자, 마침표, 하이픈 및 밑줄.<br><br>시작 및 끝은 숫자로. |
> | 네임스페이스 / Wcf릴레이 | namespace | 1-260 | 숫자, 마침표, 하이픈, 밑줄 및 슬래시.<br><br>시작 및 끝은 숫자로. |
> | 네임 스페이스 / Wcf릴레이 / 권한 부여규칙 | Wcf 릴레이 | 1-50 | 숫자, 마침표, 하이픈 및 밑줄.<br><br>시작 및 끝은 숫자로. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 배포 | 리소스 그룹 | 1-64 | 숫자, 밑줄, 괄호, 하이픈 및 마침표입니다. |
> | 리소스 그룹 | subscription | 1-90 | [정규법 문서와](/rest/api/resources/resourcegroups/createorupdate)일치하는 상숫자, 밑줄, 괄호, 하이픈, 마침표 및 유니코드 문자.<br><br>마침표로 끝날 수 없습니다. |
> | 태그 이름 | resource | 1-512 | 사용할 수 없습니다.<br>`<>%&\?/` |
> | 태그이름/ 태그값 | 태그 이름 | 1-256 | 모든 문자 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 네임스페이스 | 전역 | 6-50 | 숫자와 하이픈.<br><br>문자로 시작합니다. 문자 또는 숫자로 끝납니다.<br><br>자세한 내용은 [네임스페이스 만들기](/rest/api/servicebus/create-namespace)를 참조하십시오. |
> | 네임스페이스 / 권한 부여규칙 | namespace | 1-50 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>alphnumeric으로 시작하고 끝납니다. |
> | 네임스페이스 / 재해복구구성 | 전역 | 6-50 | 숫자와 하이픈.<br><br>문자로 시작합니다. 숫자로 끝납니다. |
> | 네임스페이스 / 마이그레이션구성 | namespace |  | 항상 **$default.** |
> | 네임스페이스/큐 | namespace | 1-260 | 숫자, 마침표, 하이픈, 밑줄 및 슬래시.<br><br>시작 및 끝은 숫자로. |
> | 네임스페이스 / 큐 / 권한 부여규칙 | queue | 1-50 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>alphnumeric으로 시작하고 끝납니다. |
> | 네임스페이스/ 토픽 | namespace | 1-260 | 숫자, 마침표, 하이픈, 밑줄 및 슬래시.<br><br>시작 및 끝은 숫자로. |
> | 네임 스페이스 / 주제 / 권한 부여규칙 | 토픽 | 1-50 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>alphnumeric으로 시작하고 끝납니다. |
> | 네임스페이스 / 토픽 / 구독 | 토픽 | 1-50 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>alphnumeric으로 시작하고 끝납니다. |
> | 네임 스페이스 / 토픽 / 구독 / 규칙 | subscription | 1-50 | 숫자, 마침표, 하이픈 및 밑줄입니다.<br><br>alphnumeric으로 시작하고 끝납니다. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 소문자, 숫자 및 하이픈.<br><br>소문자로 시작합니다. 소문자 또는 숫자로 끝납니다. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | 신호기 | 전역 | 3-63 | 숫자와 하이픈.<br><br>문자로 시작합니다. 문자 또는 번호로 끝납니다.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managedInstances | 전역 | 1-63 | 소문자, 숫자 및 하이픈.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. |
> | servers | 전역 | 1-63 | 소문자, 숫자 및 하이픈.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. |
> | 서버 / 관리자 | 서버 |  | `ActiveDirectory`이어야 합니다. |
> | 서버 / 데이터베이스 | 서버 | 1-128 | 사용할 수 없습니다.<br>`<>*%&:\/?`<br><br>마침표 나 공간으로 끝낼 수 없습니다. |
> | 서버 / 데이터베이스 / 동기화 그룹 | 데이터베이스 | 1-150 | 숫자, 하이픈 및 밑줄. |
> | 서버 / 탄성풀 | 서버 | 1-128 | 사용할 수 없습니다.<br>`<>*%&:\/?`<br><br>마침표 나 공간으로 끝낼 수 없습니다. |
> | 서버 / 장애 조치 그룹 | 전역 | 1-63 | 소문자, 숫자 및 하이픈.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. |
> | 서버 / 방화벽규칙 | 서버 | 1-128 | 사용할 수 없습니다.<br>`<>*%&:;\/?`<br><br>마침표로 끝날 수 없습니다. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | storageAccounts | 전역 | 3-24 | 소문자와 숫자. |
> | 저장소계정 / blob서비스 | 스토리지 계정 만들기 |  | `default`이어야 합니다. |
> | 저장소계정 / Blob서비스 / 컨테이너 | 스토리지 계정 만들기 | 3-63 | 소문자, 숫자 및 하이픈.<br><br>소문자 또는 숫자로 시작합니다. 연속 하이픈을 사용할 수 없습니다. |
> | 저장소계정 / 파일서비스 | 스토리지 계정 만들기 |  | `default`이어야 합니다. |
> | 저장소계정 / 파일서비스 / 공유 | 스토리지 계정 만들기 | 3-63 | 소문자, 숫자 및 하이픈.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |
> | 저장소계정/관리정책 | 스토리지 계정 만들기 |  | `default`이어야 합니다. |
> | blob | container | 1-1024 | 모든 URL 문자, 대/소문자 구분 |
> | queue | 스토리지 계정 만들기 | 3-63 | 소문자, 숫자 및 하이픈.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. 연속 하이픈을 사용할 수 없습니다. |
> | 테이블 | 스토리지 계정 만들기 | 3-63 | 숫자.<br><br>문자로 시작합니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | storageSyncServices | 리소스 그룹 | 1-260 | 영숫자, 공백, 마침표, 하이픈 및 밑줄입니다.<br><br>마침표 나 공간으로 끝낼 수 없습니다. |
> | 스토리지싱크서비스 /동기화그룹 | 스토리지 동기화 서비스 | 1-260 | 영숫자, 공백, 마침표, 하이픈 및 밑줄입니다.<br><br>마침표 나 공간으로 끝낼 수 없습니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | managers | 리소스 그룹 | 2-50 | 숫자와 하이픈.<br><br>문자로 시작합니다. 숫자로 끝납니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | streamingjobs | 리소스 그룹 | 3-63 | 숫자, 하이픈 및 밑줄. |
> | 스트리밍 작업 / 기능 | 스트리밍 작업 | 3-63 | 숫자, 하이픈 및 밑줄. |
> | 스트리밍 작업 / 입력 | 스트리밍 작업 | 3-63 | 숫자, 하이픈 및 밑줄. |
> | 스트리밍 작업 / 출력 | 스트리밍 작업 | 3-63 | 숫자, 하이픈 및 밑줄. |
> | 스트리밍작업 / 변환 | 스트리밍 작업 | 3-63 | 숫자, 하이픈 및 밑줄. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | environments | 리소스 그룹 | 1-90 | 사용할 수 없습니다.<br>`'<>%&:\?/#` |
> | 환경 / 액세스 정책 | 환경 | 1-90 | 사용할 수 없습니다.<br> `'<>%&:\?/#` |
> | 환경 / 이벤트 소스 | 환경 | 1-90 | 사용할 수 없습니다.<br>`'<>%&:\?/#` |
> | 환경 / 참조데이터 세트 | 환경 | 3-63 | 숫자 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 엔터티 | Scope | 길이 | 사용할 수 있는 문자 |
> | --- | --- | --- | --- |
> | serverfarms | 리소스 그룹 | 1-40 | 숫자와 하이픈. |
> | sites | 전역 | 2-60 | 숫자와 하이픈을 포함합니다.<br><br>하이픈으로 시작하거나 끝낼 수 없습니다. |
> | 사이트 / 슬롯 | site | 2-59 | 숫자와 하이픈. |

## <a name="next-steps"></a>다음 단계

리소스 이름을 지정하는 방법에 대한 권장 사항은 [준비: 권장 이름 지정 및 태그 지정 규칙을](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)참조하십시오.
