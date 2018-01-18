---
title: "Azure Security Center에서 OS 보안 구성 사용자 지정 [Preview] | Microsoft Docs"
description: "이 문서에서는 Security Center 평가를 사용자 지정하는 방법을 설명합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 2fa63515d290e6700fbe4a90ae509f4635b19f29
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Azure Security Center에서 OS 보안 구성 사용자 지정 [Preview]

이 연습을 사용하여 Azure Security Center에서 OS 보안 구성 평가를 사용자 지정하는 방법을 알아봅니다.

## <a name="what-are-os-security-configurations"></a>OS 보안 구성이란?

Azure Security Center는 방화벽, 감사, 암호 정책 등과 관련된 규칙을 포함하여 OS를 강화하기 위한 150여 개의 권장 규칙을 사용하여 보안 구성을 모니터링합니다. 컴퓨터에 취약한 구성이 있는 것으로 확인되면 보안 권장 사항이 생성됩니다.

규칙 사용자 지정을 통해 조직에서 해당 환경에 더 적합한 구성 옵션을 제어할 수 있습니다. 이 기능을 사용하면 사용자가 사용자 지정 평가 정책을 설정하고 구독에서 적용 가능한 모든 시스템에 적용할 수 있습니다.

> [!NOTE]
> - 현재 OS 보안 구성 사용자 지정은 Windows Server 2008, 2008R2, 2012, 2012R2 운영 체제에서만 사용할 수 있습니다.
- 구성은 선택한 구독 아래의 모든 작업 영역에 연결된 모든 VM과 컴퓨터에 적용됩니다.
- OS 보안 구성 사용자 지정은 Security Center의 표준 계층에서만 사용할 수 있습니다.
>
>

OS 보안 구성 규칙을 사용자 지정하는 방법

특정 규칙을 사용 및 사용 안 함으로 설정하고, 기존 규칙에 대한 설정을 원하는 대로 변경하고, 지원되는 규칙 유형(레지스트리, 감사 정책, 보안 정책)을 기준으로 새 규칙을 추가하여 OS 보안 구성 규칙을 사용자 지정할 수 있습니다. 현재 원하는 설정은 정확한 값이어야 합니다.

새 규칙은 동일한 유형의 다른 기존 규칙과 동일한 형식 및 구조여야 합니다.

> [!NOTE]
> OS 보안 구성을 사용자 지정하려면 구독 소유자, 구독 참가자 또는 보안 관리자의 역할이 할당되어야 합니다.
>
>

## <a name="customize-security-configuration"></a>보안 구성 사용자 지정

Security Center에서 기본 OS 보안 구성을 사용자 지정하려면

1.  **Security Center** 대시보드를 엽니다.

2.  Security Center 주 메뉴에서 **보안 정책**을 선택합니다.  **Security Center - 보안 정책**이 열립니다.

3.  사용자 지정할 구독을 선택합니다.

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. **정책 구성 요소** 아래에서 **보안 구성 편집**을 선택합니다.

6.  **보안 구성 편집**이 열립니다. 화면에서 강조 표시된 단계에 따라 파일을 다운로드 및 편집하고 수정된 파일을 업로드합니다.

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > 기본적으로 다운로드한 구성 파일은 *json* 형식입니다. 이 파일을 수정하는 방법에 대한 지침은 [구성 파일 사용자 지정](#customize-the-configuration-file)을 참조하세요.
  >

7. 파일을 저장하면 구성이 선택한 구독 아래의 모든 작업 영역에 연결된 모든 VM과 컴퓨터에 적용됩니다. 이 프로세스는 일반적으로 몇 분 정도 걸리지만 인프라 크기에 따라 시간이 더 오래 걸릴 수 있습니다. **저장**을 선택하여 변경 내용을 커밋합니다. 커밋하지 않으면 정책이 저장되지 않습니다.

    ![](media/security-center-customize-os-security-config/save-successfully.png)

언제든지 **OS 보안 구성 규칙 편집**에서 **초기화** 옵션을 선택하여 현재 정책 구성을 기본 정책 상태로 초기화할 수 있습니다. 이 옵션을 선택하면 다음과 같은 팝업 경고가 표시됩니다. **예**를 선택하여 확인합니다.

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>구성 파일 사용자 지정

사용자 지정 파일에는 지원되는 각 OS 버전에 대한 일련의 규칙(규칙 집합)이 있습니다. 다음 예제와 같이 각 규칙 집합에는 고유 이름과 고유 ID가 있습니다.

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> 이 파일은 Visual Studio를 사용하여 편집되었지만, JSON 뷰어 플러그 인이 설치되어 있을 경우 메모장을 사용할 수도 있습니다.
>
>

이 파일을 편집할 때 규칙을 하나 수정하거나 모든 규칙을 수정할 수 있습니다. 각 규칙 집합의 *규칙* 섹션에는 아래와 같이 규칙이 레지스트리, 감사 정책, 보안 정책의 세 가지 규칙 범주로 구분되어 포함됩니다.

![](media/security-center-customize-os-security-config/rules-section.png)

각 범주에 자체 특성 집합이 있습니다. 기존 규칙의 경우 다음 특성을 변경할 수 있습니다.

- expectedValue: 이 특성의 필드 데이터 형식은 각 규칙 유형에 대해 지원되는 값과 일치해야 합니다. 예를 들면 다음과 같습니다.

  - baselineRegistryRules: 이 값은 해당 규칙에 정의된 [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884(v=vs.85)와 일치해야 합니다.

  - baselineAuditPolicyRules: 이 값은 문자열 값으로, 다음 중 하나여야 합니다.

    - Success and Failure

    - 성공

  - baselineSecurityPolicyRules: 이 값은 문자열 값으로, 다음 중 하나여야 합니다.

    - “No one”

    - 허용된 사용자 그룹 목록(예: “Administrators, Backup Operators”)

-   state: “Disabled” 또는 “Enabled” 옵션을 포함할 수 있는 문자열입니다. 이 비공개 미리 보기 릴리스에서는 문자열이 대/소문자를 구분합니다.

구성할 수 있는 필드는 이뿐입니다. 파일 형식 또는 크기를 위반하면 변경 내용을 저장할 수 없습니다. 파일을 처리할 수 없는 경우 다음과 같은 오류 메시지가 표시됩니다.

![](media/security-center-customize-os-security-config/invalid-json.png)

잠재적 오류 목록은 [오류 코드](#error-codes)를 참조하세요.

변경할 수 있는 이러한 규칙과 특성의 예(굵게 표시)는 다음과 같습니다.

**규칙 섹션:** baselineRegistryRules
```
{

    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "**expectedValue**": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "**state**": "Disabled"

}
```

**규칙 섹션:** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"**expectedValue**": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"**state**": "Enabled"
},
```

**규칙 섹션:** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"**expectedValue**": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft", "**state**": "Enabled"
},
```

여러 OS 유형에서 중복되는 몇 가지 규칙이 있습니다. 중복 규칙의 ‘originalId’는 모두 같습니다.

## <a name="adding-a-new-custom-rule"></a>새 사용자 지정 규칙 추가

새 규칙을 만들 수도 있습니다. 새 규칙을 만들기 전에 다음과 같은 제한 사항에 유의하세요.

-   스키마 버전, *baselineId* 및 *baselineName*은 변경할 수 없습니다.

-   규칙 집합을 제거할 수 없습니다.

-   규칙 집합을 추가할 수 없습니다.

-   허용되는 최대 규칙 수(기본 규칙 포함)는 규칙 1000개입니다.

새 사용자 지정 규칙은 새 사용자 지정 소스(!= “Microsoft”)로 표시됩니다. *ruleId* 필드는 null이거나 비워 둘 수 있습니다. 비어 있으면 Microsoft에서 ID를 생성합니다. 비어 있지 않으면 모든 규칙(기본 및 사용자 지정)에 유효한 고유 GUID가 있어야 합니다. 핵심 필드와 관련된 아래 제약 조건을 검토합니다.

-   *originalId* - null이거나 비워 둘 수 있습니다. *originalId*가 비어 있지 않으면 유효한 GUID여야 합니다.

-   *cceId* - null이거나 비워 둘 수 있습니다. *cceId*가 비어 있지 않으면 고유해야 합니다.

-   *ruleType* - Registry, AuditPolicy 또는 SecurityPolicy 중 하나입니다.

-   *Severity* - Unknown, Critical, Warning 또는 Informational 중 하나입니다.

-   *analyzeOperation* - Equals여야 합니다.

-   *auditPolicyId* - 유효한 GUID여야 합니다.

-   *regValueType* - Int, Long, String 또는 MultipleString 중 하나여야 합니다.

> [!NOTE]
> Hive는 *LocalMachine*이어야 합니다.
>
>

새 사용자 지정 규칙의 예:

**레지스트리**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
   }
```
**보안 정책**:
```
{

   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**감사 정책:**
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>파일 업로드 오류

제출된 구성 파일이 값 또는 형식 오류로 인해 잘못된 경우 오류가 표시됩니다. 수정된 구성 파일을 다시 제출하기 전에 자세한 오류 CSV 보고서를 다운로드하여 오류를 수정할 수 있습니다.

![](media/security-center-customize-os-security-config/invalid-configuration.png)

오류 파일의 예:

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>오류 코드

아래 목록에는 모든 잠재적 오류가 나와 있습니다.

| **오류**                                | **설명**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | ‘schemaVersion’ 속성이 잘못되었거나 비어 있습니다. 값을 ‘{0}’(으)로 설정해야 합니다.                                                         |
| BaselineInvalidStringError               | ‘{0}’ 속성에 ‘\\n’을 포함할 수 없습니다.                                                                                                         |
| BaselineNullRuleError                    | 기준 구성 규칙 목록에 값이 ‘null’인 규칙이 있습니다.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID ‘{0}’이(가) 고유하지 않습니다.                                                                                                                  |
| BaselineRuleEmptyProperty                | 속성: ‘{0}’이(가) 없거나 잘못되었습니다.                                                                                                       |
| BaselineRuleIdNotInDefault               | 규칙에 소스 속성 ‘Microsoft’가 있지만 Microsoft 기본 규칙 집합에 없습니다.                                                   |
| BaselineRuleIdNotUniqueError             | 규칙 ID가 고유하지 않습니다.                                                                                                                       |
| BaselineRuleInvalidGuid                  | ‘{0}’ 속성이 잘못되었습니다. 값이 유효한 GUID가 아닙니다.                                                                             |
| BaselineRuleInvalidHive                  | Hive는 LocalMachine이어야 합니다.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | 규칙 이름이 고유하지 않습니다.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | 해당 규칙이 새 구성에 없습니다. 규칙을 삭제할 수 없습니다.                                                                     |
| BaselineRuleNotFoundError                | 해당 규칙이 기본 기준 규칙 집합에 없습니다.                                                                                        |
| BaselineRuleNotInPlace                   | 규칙이 {0} 유형의 기본 규칙과 일치하며 {1} 목록에 나열됩니다.                                                                       |
| BaselineRulePropertyTooLong              | 속성: ‘{0}’이(가) 너무 깁니다. 허용되는 최대 길이는 {1}입니다.                                                                                        |
| BaselineRuleRegTypeInvalidError          | 예상 값 ‘{0}’이(가) 정의된 레지스트리 값 형식과 일치하지 않습니다.                                                              |
| BaselineRulesetAdded                     | 기본 구성에 ID가 ‘{0}’인 규칙 집합이 없습니다. 규칙 집합을 추가할 수 없습니다.                                               |
| BaselineRulesetIdMustBeUnique            | 지정된 기준 규칙 집합 ‘{0}’이(가) 고유해야 합니다.                                                                                           |
| BaselineRulesetNotFound                  | 지정된 구성에 ID가 ‘{0}’이고 이름이 ‘{1}’인 규칙 집합이 없습니다. 규칙 집합을 삭제할 수 없습니다.                                |
| BaselineRuleSourceNotMatch               | ID가 ‘{0}’인 규칙이 이미 정의되어 있습니다.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | 기본 규칙 유형은 ‘{0}’입니다.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | 규칙의 실제 유형이 {0}인데 ruleType 속성이 {1}입니다.                                                                          |
| BaselineRuleUnpermittedChangesError      | ‘expectedValue’ 및 ‘status’ 속성만 변경할 수 있습니다.                                                                       |
| BaselineTooManyRules                     | 허용되는 최대 사용자 지정 규칙 수는 규칙 {0}개입니다. 지정된 구성에 규칙 {1}개가 포함되어 있습니다. 기본 규칙 {2}개 + 사용자 지정 규칙 {3}개입니다. |
| ErrorNoConfigurationStatus               | 구성 상태가 없습니다. 원하는 구성 상태(‘Default’ 또는 ‘Custom’)를 설정하세요.                                    |
| ErrorNonEmptyRulesetOnDefault            | 구성 상태가 Default로 설정되어 있습니다. BaselineRulesets 목록은 null이거나 비어 있어야 합니다.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | 지정된 구성 상태가 ‘Custom’인데 ‘baselineRulesets’ 속성이 null이거나 비어 있습니다.                                             |
| ErrorParsingBaselineConfig               | 지정된 구성이 잘못되었습니다. 정의된 하나 이상의 값이 null 값이거나 잘못된 형식입니다.                                  |
| ErrorParsingIsDefaultProperty            | 지정된 ‘configurationStatus’ 값 ‘{0}’이(가) 잘못되었습니다. 값에 ‘Default’ 또는 ‘Custom’만 지정할 수 있습니다.                                         |
| InCompatibleViewVersion                  | 뷰 버전: {0}은(는) 이 작업 영역 유형에서 지원되지 않습니다.                                                                                   |
| InvalidBaselineConfigurationGeneralError | 지정된 기준 구성에 하나 이상의 형식 유효성 검사 오류가 있습니다.                                                          |
| ViewConversionError                      | 뷰가 작업 영역에서 지원되는 버전보다 이전 버전입니다. 뷰 변환 실패: {0}                                                                 |

충분한 권한이 없을 경우 일반 오류가 표시될 수 있습니다.

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 OS 보안 구성 평가를 사용자 지정하는 방법을 살펴보았습니다. 구성 규칙 및 수정에 대해 자세히 알아보려면 다음을 참조하세요.

- [Security Center 공통 구성 식별자 및 기준 규칙](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- 보안 센터는 일반 구성 열거형(CCE)을 사용하여 구성 규칙에 대한 고유 ID를 할당합니다. 자세한 내용은 [CCE](https://nvd.nist.gov/config/cce/index) 사이트를 방문하세요.
- [보안 구성 수정](security-center-remediate-os-vulnerabilities.md)에서는 OS 구성이 권장 보안 구성 규칙과 일치하지 않을 경우 취약점을 해결하는 방법을 보여 줍니다.
