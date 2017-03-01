---
title: "Microsoft 위협 모델링 도구 - Azure | Microsoft Docs"
description: "Microsoft 위협 모델링 도구에 대한 기본 페이지로, 가장 큰 노출 위협에 대한 해결 방법을 포함합니다."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Microsoft 위협 모델링 도구 
위협 모델링 도구는 Microsoft SDL(Security Development Lifecycle)의 핵심 요소입니다. 소프트웨어 설계자는 이 도구를 사용하여 비교적 쉽고 비용 효과적으로 문제를 해결할 수 있는 조기에 잠재적인 보안 문제를 식별하고 완화할 수 있습니다. 결과적으로 전체 개발 비용이 크게 절감됩니다. 또한 보안 전문가가 아닌 사용자를 염두에 두고 개발되었으므로 위협 모델을 만들고 분석하기 위한 정확한 지침을 제공하여 모든 개발자가 쉽게 위협 모델링을 수행할 수 있도록 합니다. 

이 도구를 사용하면 누구나 다음을 수행할 수 있습니다.
* 시스템의 보안 설계에 대한 기본 사항 전달
* 입증된 방법을 사용하여 이러한 설계의 잠재적인 보안 문제 분석
* 보안 문제에 대한 해결 방법 제안 및 관리

일부 도구 기능 및 혁신 기능은 다음과 같습니다.
* **자동화:** 모형 그리기에 대한 지침 및 피드백
* **요소 기준 STRIDE:** 위협 및 해결 방법에 대한 단계별 분석
* **보고:** 확인 단계의 보안 작업 및 테스트
* **고유한 방법:** 위협을 보다 잘 시각화하고 이해하도록 지원
* **소프트웨어 중심의 개발자를 위한 설계:** 많은 접근 방식이 자산 또는 공격자에 주안점을 둡니다. Microsoft는 소프트웨어 중심 방식을 제공합니다. 소프트웨어 아키텍처에 대한 그림 그리기와 같이 모든 소프트웨어 개발자와 설계자에게 친숙한 작업을 토대로 기능을 구현하고 있습니다.
* **설계 분석에 주력:** "위협 모델링"이라는 용어는 요구 사항 또는 설계 분석 기법을 나타낼 수 있습니다. 경우에 따라 두 가지가 복잡하게 혼합된 경우를 나타냅니다. Microsoft SDL의 위협 모델링 방법은 설계 분석 기법에 초점을 맞춥니다.

## <a name="threats"></a>위협

위협 모델링 도구를 사용하면 아래와 같은 특정 질문에 답할 수 있습니다.

* 공격자는 어떤 방식으로 인증 데이터를 변경하나요?
* 공격자가 사용자 프로필 데이터를 읽을 수 있으면 어떤 영향을 미치나요?
* 사용자 프로필 데이터베이스에 대한 액세스가 거부되면 어떻게 되나요?

이와 같이 핵심을 찌르는 질문을 보다 잘 만들기 위해 Microsoft는 STRIDE 모델을 사용합니다. 이 모델은 다양한 유형의 위협을 분류하고 전반적인 보안 대화를 간소화합니다.

| Category | 설명 |
| -------- | ----------- |
| 스푸핑 | 사용자 이름 및 암호와 같은 다른 사용자의 인증 정보를 불법적으로 액세스하고 사용하는 경우를 나타냅니다. |
| 변조 | 데이터의 악의적인 수정을 나타냅니다. 데이터베이스에 포함된 것과 같은 영구 데이터를 무단으로 변경하거나, 인터넷과 같은 개방된 네트워크를 통해 두 컴퓨터 간에 전달되는 데이터를 변경하는 경우 등이 여기에 해당합니다. |
| 거부 | 다른 당사자에게 입증 방법을 제공하지 않고 작업 수행을 거부하는 사용자와 관련된 방식입니다. 예를 들어 사용자는 금지된 작업을 추적하는 기능이 부족한 시스템에서 불법 작업을 수행할 수 있습니다. 거부 방지는 거부 위협을 발생하는 시스템의 기능을 말합니다. 예를 들어 항목을 구입하는 사용자는 항목을 받을 때 서명해야 할 수 있습니다. 그러면 공급업체는 서명된 영수증을 사용자가 포장 제품을 받았다는 증명 정보로 사용할 수 있습니다. |
| 정보 공개 | 액세스하지 않아야 하는 개인에게 정보가 노출되는 경우를 나타냅니다. 예를 들어 사용자에게 액세스 권한이 없는 파일을 읽을 능력이 부여되거나 침입자가 두 컴퓨터 간에 전송되는 데이터를 읽을 수 있는 경우가 여기에 해당합니다. |
| 서비스 거부 | DoS(서비스 거부) 공격은 예를 들어 웹 서버를 일시적으로 사용할 수 없게 만들어 유효한 사용자에 대해 서비스 거부 공격을 수행합니다. 특정 유형의 DoS 위협으로부터 보호하기만 해도 시스템 가용성과 안정성이 개선됩니다. |
| 권한 상승 | 권한 없는 사용자가 권한 있는 액세스를 얻게 되면 전체 시스템을 손상시키거나 파괴할 수 있게 됩니다. 권한 상승 위협은 공격자가 모든 시스템 방어망을 효과적으로 침투하고 신뢰할 수 있는 시스템 자체의 일부가 되는 상황을 야기하므로 실제로 굉장히 위험합니다. |

## <a name="mitigations"></a>해결 방법

위협 모델링 도구 해결 방법은 다음으로 구성된 웹 응용 프로그램 보안 프레임에 따라 분류됩니다.

| Category | 설명 |
| -------- | ----------- |
| [감사 및 로깅](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | 누가 언제 무엇을 했나요? 감사 및 로깅은 응용 프로그램이 보안 관련 이벤트를 기록하는 방법을 나타냅니다. |
| [인증](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | 당신은 누구인가요? 인증은 엔터티가 일반적으로 사용자 이름 및 암호와 같은 자격 증명을 통해 다른 엔터티의 ID를 증명하는 과정입니다. |
| [권한 부여](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | 어떻게 해야 합니까? 권한 부여는 응용 프로그램이 리소스 및 작업에 대한 액세스 제어를 제공하는 방법입니다. | 
| [통신 보안](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | 누구에게 이야기하고 있나요? 통신 보안은 수행되는 모든 통신을 최대한 안전하게 유지합니다. |
| [구성 관리](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | 응용 프로그램이 어떤 권한으로 실행되고 있나요? 어떤 데이터베이스에 연결되나요? 응용 프로그램이 어떤 방식으로 관리되나요? 이러한 설정은 어떻게 보호되나요? 구성 관리는 응용 프로그램이 이러한 운영 문제를 처리하는 방법을 나타냅니다. | 
| [암호화](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | 비밀(기밀성)은 어떻게 유지하나요? 데이터 또는 라이브러리의 변조를 방지하려면 어떻게 하나요(무결성)? 암호로 강력하게 보호해야 하는 임의 값에 대해 시드를 제공하려면 어떻게 해야 하나요? 암호화는 응용 프로그램이 기밀성 및 무결성을 적용하는 방법을 나타냅니다. | 
| [예외 관리](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | 응용 프로그램의 메서드 호출이 실패하면 응용 프로그램은 어떻게 하나요? 얼마나 많은 정보를 노출하고 있나요? 최종 사용자에게 오류 정보를 친절하게 반환하고 있나요? 중요한 예외 정보를 호출자에게 다시 제공하고 있나요? 응용 프로그램이 정상적으로 실패하나요? |
| [입력 유효성 검사](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | 응용 프로그램이 수신하는 입력이 유효하고 안전한지를 어떻게 알 수 있나요? 입력 유효성 검사는 응용 프로그램이 입력을 추가로 처리하기 전에 필터링, 삭제 또는 거절하는 방법을 나타냅니다. 진입점을 통해 입력을 제한하고 종료 지점을 통과하는 출력을 인코딩하는 것이 좋습니다. 데이터베이스 및 파일 공유와 같은 원본의 데이터를 신뢰할 수 있나요? |
| [중요 데이터](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | 응용 프로그램은 중요한 데이터를 어떻게 처리하나요? 중요한 데이터는 응용 프로그램에서 메모리 내에, 네트워크를 통해 또는 영구 저장소에서 보호되어야 하는 모든 데이터를 처리하는 방법을 나타냅니다. | 
| [세션 관리](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | 응용 프로그램은 사용자 세션을 어떻게 처리하고 보호하나요? 세션은 사용자와 웹 응용 프로그램 간에 관련된 일련의 상호 작용입니다. | 

세션을 다음을 파악하는 데 도움이 됩니다. 

* 가장 일반적인 실수가 수행되는 위치
* 실행 가능한 개선 방법이 제공되는 위치

결과적으로 이러한 범주를 사용하여 보안 작업에 주력하고 우선 순위를 두고 있고, 입력 유효성 검사, 인증 및 권한 부여 범주에서 보안 문제가 가장 널리 발생한다는 사실을 알 수 있으므로 여기서부터 시작할 수 있습니다. 자세한 내용을 보려면 [여기](https://www.google.com/patents/US7818788)를 클릭하세요.

## <a name="next-steps"></a>다음 단계
다음의 유용한 링크를 확인하여 지금 바로 시작하세요.
* [다운로드 링크](https://www.microsoft.com/download/details.aspx?id=49168)
* [시작](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [핵심 교육](https://www.microsoft.com/download/details.aspx?id=16420)

준비가 되면 몇 가지 위협 모델링 도구 전문가가 수행한 작업을 확인해 보세요.
* [위협 관리자](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi 보안 블로그](https://simoneonsecurity.com/)
