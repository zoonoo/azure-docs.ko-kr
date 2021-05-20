---
title: Azure Sentinel의 고급 다단계 공격 감지
description: Azure Sentinel의 Fusion 기술을 사용하여 경고 피로를 줄이고 고급 다단계 공격 감지를 기반으로 하는 실행 가능한 인시던트를 만듭니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724288"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel의 고급 다단계 공격 감지

> [!IMPORTANT]
> 일부 Fusion 감지(아래 참조)는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel은 기계 학습 기반의 Fusion 기술을 사용하면 킬 체인의 다양한 단계에서 관찰되는 비정상 동작과 의심스러운 활동을 식별하여 자동으로 다단계 공격을 감지할 수 있습니다. 이렇게 찾은 내용을 기반으로 Azure Sentinel은 놓치기 쉬운 인시던트를 생성합니다. 이러한 인시던트는 둘 이상의 경고 또는 활동으로 구성됩니다. 설계 의도에 따라 이러한 인시던트는 볼륨이 작고, 충실도가 높고, 심각도가 높습니다.

환경에 맞게 사용자 지정되는 이 감지 기술은 가양성 비율을 줄일 뿐 아니라 정보가 제한되거나 누락된 공격도 감지할 수 있습니다.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>고급 다단계 공격 검색 구성

이 감지 기능은 Azure Sentinel에서 기본적으로 사용하도록 설정됩니다. 상태를 확인하거나, 대체 솔루션을 사용하여 여러 경고를 기반으로 인시던트를 만들고 있어서 이 감지 기능을 사용하지 않도록 설정하려면 다음 지침을 따릅니다.

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. **Azure Sentinel** > **구성** > **분석** 으로 이동합니다.

1. **활성 규칙** 을 선택한 다음, **Fusion** 규칙 유형에 대한 목록을 필터링하여 **이름** 열에서 **고급 다단계 공격 감지** 를 찾습니다. **상태** 열을 확인하여 감지 사용 여부를 확인합니다.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{대체 텍스트}":::

1. 상태를 변경하려면 이 항목을 선택하고 **고급 다단계 공격 감지** 블레이드에서 **편집** 을 선택합니다.

1. **규칙 만들기 마법사** 블레이드에서 상태 변경이 자동으로 선택됩니다. **다음: 검토** 를 선택하고 **저장** 을 선택합니다. 

 **Fusion** 규칙 형식에는 수정할 수 없는 규칙 하나만 포함되어 있으므로 이 규칙 형식에는 규칙 템플릿을 적용할 수 없습니다.

> [!NOTE]
> Azure Sentinel은 현재 30일의 기록 데이터를 사용하여 기계 학습 시스템을 학습시킵니다. 이 데이터는 기계 학습 파이프라인을 통과할 때 항상 Microsoft의 키를 사용하여 암호화됩니다. 그러나 Azure Sentinel 작업 영역에서 CMK를 사용하도록 설정한 경우에는 학습 데이터가 [CMK(고객 관리형 키)](customer-managed-keys.md)를 사용하여 암호화되지 않습니다. Fusion을 옵트아웃하려면 **Azure Sentinel** \> **구성** \> **분석 \> 활성 규칙 \> 고급 다단계 공격 감지** 로 이동한 다음, **상태** 열에서 **사용 안 함** 을 선택합니다.

## <a name="attack-detection-scenarios"></a>공격 감지 시나리오

다음 섹션에서는 Azure Sentinel이 Fusion 기술을 사용하여 찾는 상관 관계 시나리오 유형을 위협 분류에 따라 그룹화하여 보여줍니다.

위에서 언급했듯이, Fusion은 다양한 제품의 여러 보안 경고 간에 상관 관계를 지정하여 고급 다단계 공격을 감지하므로, 성공적인 Fusion 감지는 Azure Sentinel **인시던트** 페이지에 **Fusion 인시던트** 로 표시되고 **로그** 의 **보안 경고** 테이블에는 **경고** 로 표시되지 않습니다.

이러한 Fusion 기반 공격 감지 시나리오를 지원하려면 연결된 Azure Sentinel 데이터 커넥터를 사용하여 나열된 모든 데이터 원본을 수집해야 합니다.

> [!NOTE]
> 이러한 시나리오 중 일부는 **미리 보기** 이며 미리 보기라고 표시됩니다.

## <a name="compute-resource-abuse"></a>컴퓨팅 리소스 남용

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>의심스러운 Azure Active Directory 로그인 후 여러 VM 만들기 활동
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 영향 

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 리소스 하이재킹(T1496)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 단일 세션에서 비정상적인 수의 VM이 생성되었음을 나타냅니다. 이 유형의 경고는 Fusion 인시던트 설명에 언급된 계정이 손상되어 암호화 마이닝 작업 실행과 같이 무단으로 새 VM을 만드는 데 사용되고 있음을 강하게 시사합니다. 여러 VM 만들기 활동 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **비정상적 위치로 불가능한 이동 후 여러 VM 만들기 활동**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 여러 VM 만들기 활동**

- **감염된 디바이스에서 로그인 이벤트 발생 후 여러 VM 만들기 활동**

- **익명 IP 주소에서 로그인 이벤트 발생 후 여러 VM 만들기 활동**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 여러 VM 만들기 활동**

## <a name="credential-harvesting-new-threat-classification"></a>자격 증명 수집(새로운 위협 분류)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>의심스러운 로그인 후 악성 자격 증명 유출 실행

**MITRE ATT&CK 전술:** 초기 액세스, 자격 증명 액세스

**MITRE ATT&CK 기술:** 유효한 계정(T1078), OS 자격 증명 덤핑(T1003)

**데이터 커넥터 원본:** Azure Active Directory Identity Protection, 엔드포인트용 Microsoft Defender

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 로그인 후 알려진 자격 증명 유출 도구가 실행되었음을 나타냅니다. 경고 설명에 언급된 사용자 계정이 손상되었으며 **Mimikatz** 같은 도구를 사용하여 시스템의 키, 일반 텍스트 암호 및/또는 암호 해시와 같은 자격 증명을 수집하는 데 사용되었음을 강하게 시사합니다. 수집된 자격 증명을 통해 공격자는 중요한 데이터에 액세스하고, 권한을 높이고, 네트워크를 수평으로 이동할 수 있습니다. 악성 자격 증명 유출 도구 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **비정상적 위치로 불가능한 이동 후 악성 자격 증명 유출 도구 실행**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 악성 자격 증명 유출 도구 실행**

- **감염된 디바이스에서 로그인 이벤트 발생 후 악성 자격 증명 유출 도구 실행**

- **익명 IP 주소에서 로그인 이벤트 발생 후 악성 자격 증명 유출 도구 실행**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 악성 자격 증명 유출 도구 실행**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>의심스러운 로그인 후 의심스러운 자격 증명 유출 활동

**MITRE ATT&CK 전술:** 초기 액세스, 자격 증명 액세스

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 암호 저장소의 자격 증명(T1555), OS 자격 증명 덤핑(T1003)

**데이터 커넥터 원본:** Azure Active Directory Identity Protection, 엔드포인트용 Microsoft Defender

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 로그인 후에 발생하는 자격 증명 유출 패턴과 관련된 활동이 있음을 나타냅니다. 경고 설명에 언급된 사용자 계정이 손상되었으며 키, 일반 텍스트 암호, 암호 해시 등의 자격 증명을 유출하는 데 사용되었음을 강하게 시사합니다. 훔친 자격 증명을 통해 공격자는 중요한 데이터에 액세스하고, 권한을 높이고, 네트워크를 수평으로 이동할 수 있습니다. 자격 증명 유출 활동 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **비정상적 위치로 불가능한 이동 후 의심스러운 자격 증명 유출 활동**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 의심스러운 자격 증명 유출 활동**

- **감염된 디바이스에서 로그인 이벤트 발생 후 의심스러운 자격 증명 유출 활동**

- **익명 IP 주소에서 로그인 이벤트 발생 후 의심스러운 자격 증명 유출 활동**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 의심스러운 자격 증명 유출 활동**

## <a name="crypto-mining-new-threat-classification"></a>암호화-마이닝(새로운 위협 분류)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>의심스러운 로그인 후 암호화 마이닝 활동

**MITRE ATT&CK 전술:** 초기 액세스, 자격 증명 액세스

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 리소스 하이재킹(T1496)

**데이터 커넥터 원본:** Azure Active Directory Identity Protection, Azure Defender(Azure Security Center)

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인과 연결된 암호화 마이닝 활동을 나타냅니다. 경고 설명에 언급된 사용자 계정이 손상되었으며 암호화 통화를 마이닝하기 위해 환경의 리소스를 가로채는 데 사용되었음을 강하게 시사합니다. 이로 인해 컴퓨팅 성능 리소스가 부족할 수 있으며 클라우드 사용 요금이 예상보다 훨씬 많이 청구될 수 있습니다. 암호화 마이닝 활동 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 암호화 마이닝 활동**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 암호화 마이닝 활동**

- **감염된 디바이스에서 로그인 이벤트 발생 후 암호화 마이닝 활동**

- **익명 IP 주소에서 로그인 이벤트 발생 후 암호화 마이닝 활동**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 암호화 마이닝 활동**

## <a name="data-exfiltration"></a>데이터 반출

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 Office 365 사서함 반출

**MITRE ATT&CK 전술:** 초기 액세스, 반출, 수집

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 이메일 수집(T1114), 자동 반출(T1020)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 사용자의 받은 편지함에서 의심스러운 받은 편지함 전달 규칙이 설정되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 사용자 계정이 손상되었으며 실제 사용자 몰래 받은 편지함 전달 규칙을 사용하도록 설정하여 조직의 네트워크에서 데이터를 반출하는 데 사용되었음을 강하게 시사합니다. Office 365 받은 편지함 반출 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **비정상적 위치로 불가능한 이동 후 Office 365 받은 편지함 반출**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 Office 365 받은 편지함 반출**

- **감염된 디바이스에서 로그인 이벤트 발생 후 Office 365 받은 편지함 반출**

- **익명 IP 주소에서 로그인 이벤트 발생 후 Office 365 받은 편지함 반출**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 Office 365 받은 편지함 반출**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 대량 파일 다운로드

**MITRE ATT&CK 전술:** 초기 액세스, 반출

**MITRE ATT&CK 기술:** 유효한 계정(T1078)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 비정상적인 수의 파일이 다운로드되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었으며 조직의 네트워크에서 데이터를 반출하는 데 사용되었음을 강하게 시사합니다. 대량 파일 다운로드 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 대량 파일 다운로드**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 대량 파일 다운로드**

- **감염된 디바이스에서 로그인 이벤트 발생 후 대량 파일 다운로드**

- **익명 IP 주소에서 로그인 이벤트 발생 후 대량 파일 다운로드**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 대량 파일 다운로드**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 대량 파일 공유

**MITRE ATT&CK 전술:** 초기 액세스, 반출

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 웹 서비스를 통해 반출(T1567)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 임계값을 초과하는 수의 파일이 다른 사람과 공유되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었으며 악의적인 목적을 가진 권한 없는 사용자와 문서, 스프레드시트 등의 파일을 공유하여 조직 네트워크에서 데이터를 반출하는 데 사용되었음을 강하게 시사합니다. 대량 파일 공유 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 대량 파일 공유**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 대량 파일 공유**

- **감염된 디바이스에서 로그인 이벤트 발생 후 대량 파일 공유**

- **익명 IP 주소에서 로그인 이벤트 발생 후 대량 파일 공유**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 대량 파일 공유**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 의심스러운 받은 편지함 조작 규칙 설정
이 시나리오는 **데이터 반출** 및 **수평 이동** 이라는 이 목록의 두 가지 위협 분류에 속합니다. 명확한 이해를 돕기 위해 두 섹션에 모두 표시됩니다.

이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 수평 이동, 반출

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 내부 스피어 피싱(T1534)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 사용자의 받은 편지함에서 비정상적인 받은 편지함 규칙이 설정되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었으며 악의적인 목적으로 사용자의 이메일 받은 편지함 규칙을 조작하는 데 사용되었음을 강하게 시사합니다. 공격자가 조직의 네트워크에서 데이터를 반출하려고 시도하는 것일 수 있습니다. 또는 공격자가 추가 사용자 및/또는 권한 있는 계정의 액세스 권한을 획득하여 수평 이동할 목적으로 조직 내에서(외부 소스의 이메일을 대상으로 하는 피싱 감지 메커니즘 회피) 피싱 이메일을 생성하려고 시도하는 것일 수 있습니다. 의심스러운 받은 편지함 조작 규칙 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 의심스러운 받은 편지함 조작 규칙**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

- **감염된 디바이스에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

- **익명 IP 주소에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 여러 Power BI 보고서 공유 활동 
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 반출 

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 웹 서비스를 통해 반출(T1567)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 단일 세션에서 비정상적인 수의 Power BI 보고서가 공유되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었으며 악의적인 목적을 가진 권한 없는 사용자와 Power BI 보고서를 공유하여 조직 네트워크에서 데이터를 반출하는 데 사용되었음을 강하게 시사합니다. 여러 Power BI 보고서 공유 활동이 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 여러 Power BI 보고서 공유 활동**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 여러 Power BI 보고서 공유 활동**

- **감염된 디바이스에서 로그인 이벤트 발생 후 여러 Power BI 보고서 공유 활동**

- **익명 IP 주소에서 로그인 이벤트 발생 후 여러 Power BI 보고서 공유 활동**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 여러 Power BI 보고서 공유 활동**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 의심스러운 Power BI 보고서 공유
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 반출 

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 웹 서비스를 통해 반출(T1567)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 의심스러운 Power BI 보고서 공유 활동이 발생했음을 나타냅니다. Power BI 보고서에는 자연어 처리를 사용하여 식별된 중요한 정보가 포함되어 있는데 이러한 보고서가 외부 이메일 주소와 공유되거나, 웹에 게시되거나, 외부에서 구독한 이메일 주소에 스냅샷으로 전달되었기 때문에 의심스러운 공유 활동으로 식별되었습니다. 이 경고는 Fusion 인시던트 설명에 언급된 계정이 손상되었으며 악의적인 목적을 가진 권한 없는 사용자와 Power BI 보고서를 공유하여 조직 네트워크에서 데이터를 반출하는 데 사용되었음을 강하게 시사합니다. 의심스러운 Power BI 보고서 공유가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 의심스러운 Power BI 보고서 공유**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 의심스러운 Power BI 보고서 공유**

- **감염된 디바이스에서 로그인 이벤트 발생 후 의심스러운 Power BI 보고서 공유**

- **익명 IP 주소에서 로그인 이벤트 발생 후 의심스러운 Power BI 보고서 공유**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 의심스러운 Power BI 보고서 공유**

## <a name="data-destruction"></a>데이터 폐기

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 대량 파일 삭제

**MITRE ATT&CK 전술:** 초기 액세스, 영향

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 데이터 삭제(T1485)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 비정상적인 수의 고유한 파일이 삭제되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었을 수 있으며 악의적인 목적으로 데이터를 제거하는 데 사용되었을 가능성이 매우 높다는 것을 나타냅니다. 대량 파일 삭제 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 대량 파일 삭제**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 대량 파일 삭제**

- **감염된 디바이스에서 로그인 이벤트 발생 후 대량 파일 삭제**

- **익명 IP 주소에서 로그인 이벤트 발생 후 대량 파일 삭제**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 대량 파일 삭제**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 의심스러운 이메일 삭제 활동
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 영향 

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 데이터 삭제(T1485)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 단일 세션에서 비정상적인 수의 이메일이 삭제되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었을 수 있으며 조직에 피해를 입히거나 스팸 관련 이메일 활동을 숨기는 등의 악의적인 목적으로 데이터를 삭제하는 데 사용되었음을 나타냅니다. 의심스러운 이메일 삭제 활동 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.   

- **비정상적 위치로 불가능한 이동 후 의심스러운 이메일 삭제 활동**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 의심스러운 이메일 삭제 활동**

- **감염된 디바이스에서 로그인 이벤트 발생 후 의심스러운 이메일 삭제 활동**

- **익명 IP 주소에서 로그인 이벤트 발생 후 의심스러운 이메일 삭제 활동**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 의심스러운 이메일 삭제 활동**

## <a name="denial-of-service"></a>서비스 거부

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 여러 VM 삭제 활동
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 영향

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 엔드포인트 서비스 거부(T1499)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 단일 세션에서 비정상적인 수의 VM이 삭제되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었으며 조직의 클라우드 환경을 중단하거나 파괴하려는 시도에 사용되었음을 강하게 시사합니다. 여러 VM 삭제 활동 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 여러 VM 삭제 활동**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 여러 VM 삭제 활동**

- **감염된 디바이스에서 로그인 이벤트 발생 후 여러 VM 삭제 활동**

- **익명 IP 주소에서 로그인 이벤트 발생 후 여러 VM 삭제 활동**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 여러 VM 삭제 활동**

## <a name="lateral-movement"></a>수평 이동

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 Office 365 가장

**MITRE ATT&CK 전술:** 초기 액세스, 수평 이동

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 내부 스피어 피싱(T1534)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 비정상적인 수의 가장 작업이 발생했음을 나타냅니다. 일부 소프트웨어는 사용자가 다른 사용자를 가장할 수 있도록 허용하는 옵션이 있습니다. 예를 들어 사용자는 이메일 서비스에서 다른 사용자에게 본인 대신 이메일을 보낼 수 있도록 권한을 부여할 수 있습니다. 이 경고는 Fusion 인시던트 설명에 언급된 계정이 손상되었으며 맬웨어 배포 또는 수평 이동을 위해 피싱 이메일을 보내는 등의 악의적인 목적으로 가장 활동을 수행하는 데 사용되었음을 강하게 시사합니다. Office 365 가장 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 Office 365 가장**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 Office 365 가장**

- **감염된 디바이스에서 로그인 이벤트 발생 후 Office 365 가장**

- **익명 IP 주소에서 로그인 이벤트 발생 후 Office 365 가장**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 Office 365 가장**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 의심스러운 받은 편지함 조작 규칙 설정
이 시나리오는 **수평 이동** 및 **데이터 반출** 이라는 이 목록의 두 가지 위협 분류에 속합니다. 명확한 이해를 돕기 위해 두 섹션에 모두 표시됩니다.

이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 수평 이동, 반출

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 내부 스피어 피싱(T1534), 자동 반출(T1020)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 사용자의 받은 편지함에서 비정상적인 받은 편지함 규칙이 설정되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었으며 악의적인 목적으로 사용자의 이메일 받은 편지함 규칙을 조작하는 데 사용되었음을 강하게 시사합니다. 공격자가 조직의 네트워크에서 데이터를 반출하려고 시도하는 것일 수 있습니다. 또는 공격자가 추가 사용자 및/또는 권한 있는 계정의 액세스 권한을 획득하여 수평 이동할 목적으로 조직 내에서(외부 소스의 이메일을 대상으로 하는 피싱 감지 메커니즘 회피) 피싱 이메일을 생성하려고 시도하는 것일 수 있습니다. 의심스러운 받은 편지함 조작 규칙 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **비정상적 위치로 불가능한 이동 후 의심스러운 받은 편지함 조작 규칙**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

- **감염된 디바이스에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

- **익명 IP 주소에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 의심스러운 받은 편지함 조작 규칙**

## <a name="malicious-administrative-activity"></a>악의적인 관리 활동

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 의심스러운 클라우드 앱 관리 활동

**MITRE ATT&CK tactics:** 초기 액세스, 지속성, 방어 회피, 수평 이동, 수집, 반출 및 영향

**MITRE ATT&CK 기술:** 해당 없음

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 동일한 계정에서 의심스러운 Azure AD 로그인 후 단일 세션에서 비정상적인 수의 관리 활동을 수행했음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었을 수 있으며 악의적인 목적으로 무단 관리 활동을 수행하는 데 사용되었음을 나타냅니다. 또한 관리자 권한이 있는 계정이 손상되었을 수 있음을 나타냅니다. 의심스러운 클라우드 앱 관리 활동 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 의심스러운 클라우드 앱 관리 활동**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 의심스러운 클라우드 앱 관리 활동**

- **감염된 디바이스에서 로그인 이벤트 발생 후 의심스러운 클라우드 앱 관리 활동**

- **익명 IP 주소에서 로그인 이벤트 발생 후 의심스러운 클라우드 앱 관리 활동**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 의심스러운 클라우드 앱 관리 활동**

## <a name="malicious-execution-with-legitimate-process"></a>합법적인 프로세스를 통해 악의적 실행

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell에서 의심스러운 네트워크 연결을 설정한 후 Palo Alto Networks 방화벽이 비정상적인 트래픽에 플래그 지정
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 실행

**MITRE ATT&CK 기술:** 명령 및 스크립팅 인터프리터(T1059)

**데이터 커넥터 원본:** 엔드포인트용 Microsoft Defender(이전에는 MDATP(Microsoft Defender Advanced Threat Protection)), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 PowerShell 명령을 통해 아웃바운드 연결 요청이 수행된 후 Palo Alto Networks 방화벽이 비정상적인 인바운드 활동을 감지했음을 나타냅니다. 공격자가 네트워크 액세스 권한을 획득하여 악의적인 작업을 수행하려고 시도 중일 가능성이 높다는 것을 나타냅니다. 이 패턴을 따르는 PowerShell의 연결 시도는 맬웨어 명령 및 제어 활동, 추가 맬웨어 다운로드 요청 또는 공격자가 원격 대화형 액세스를 설정하려 한다는 것을 의미할 수 있습니다. 모든 "living off the land(지상 생활)" 공격과 마찬가지로, 이 활동은 PowerShell을 합법적으로 사용할 수 있습니다. 그러나 PowerShell 명령 실행 후 의심스러운 인바운드 방화벽 활동이 발생하면 PowerShell이 악의적인 목적으로 사용되고 있을 가능성이 높으므로 추가 조사가 필요합니다. Palo Alto 로그에서 Azure Sentinel은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용되면 트래픽이 의심스러운 것으로 간주됩니다(의심스러운 데이터, 파일, 홍수, 패킷, 검사, 스파이웨어, URL, 바이러스, 취약성, wildfire-바이러스, wildfires). 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열된 [위협/콘텐츠 형식](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)에 해당하는 Palo Alto 위협 로그를 참조하세요.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>의심스러운 원격 WMI 실행 후 Palo Alto Networks 방화벽이 비정상적인 트래픽에 플래그 지정
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 실행, 발견

**MITRE ATT&CK 기술:** Windows Management Instrumentation(T1047)

**데이터 커넥터 원본:** 엔드포인트용 Microsoft Defender(이전에는 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 WMI(Windows Management Interface) 명령이 시스템에서 원격으로 실행되었으며, 그 후 Palo Alto Networks 방화벽이 의심스러운 인바운드 활동을 감지했음을 나타냅니다. 공격자가 네트워크 액세스 권한을 획득하여 수평으로 이동하고, 권한을 높이고, 악의적인 페이로드를 실행하려고 시도 중일 수 있음을 나타냅니다. 모든 "living off the land(지상 생활)" 공격과 마찬가지로, 이 활동은 WMI를 합법적으로 사용할 수 있습니다. 그러나 원격 WMI 명령 실행 후 의심스러운 인바운드 방화벽 활동이 발생하면 WMI가 악의적인 목적으로 사용되고 있을 가능성이 높으므로 추가 조사가 필요합니다. Palo Alto 로그에서 Azure Sentinel은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용되면 트래픽이 의심스러운 것으로 간주됩니다(의심스러운 데이터, 파일, 홍수, 패킷, 검사, 스파이웨어, URL, 바이러스, 취약성, wildfire-바이러스, wildfires). 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열된 [위협/콘텐츠 형식](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)에 해당하는 Palo Alto 위협 로그를 참조하세요.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>의심스러운 로그인 후 의심스러운 PowerShell 명령줄

**MITRE ATT&CK 전술:** 초기 액세스, 실행

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 명령 및 스크립팅 인터프리터(T1059)

**데이터 커넥터 원본:** Azure Active Directory Identity Protection, 엔드포인트용 Microsoft Defender(이전에는 MDATP)

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 사용자가 잠재적 악성 PowerShell 명령을 실행했음을 나타냅니다. 경고 설명에 언급된 계정이 손상되었으며 그 외에도 악의적 활동이 수행되었음을 강하게 시사합니다. 공격자는 바이러스 검색 프로그램 같은 디스크 기반 보안 메커니즘을 피하기 위해 종종 PowerShell을 활용하여 디스크에 아티팩트를 남기지 않고 메모리에서 악성 페이로드를 실행합니다. 의심스러운 PowerShell 명령 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.

- **비정상적 위치로 불가능한 이동 후 의심스러운 PowerShell 명령줄**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 의심스러운 PowerShell 명령줄**

- **감염된 디바이스에서 로그인 이벤트 발생 후 의심스러운 PowerShell 명령줄**

- **익명 IP 주소에서 로그인 이벤트 발생 후 의심스러운 PowerShell 명령줄**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 의심스러운 PowerShell 명령줄**

## <a name="malware-c2-or-download"></a>맬웨어 C2 또는 다운로드

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 익명화 서비스에 대한 네트워크 요청 후 Palo Alto Networks 방화벽이 비정상적인 트래픽에 플래그 지정
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 명령 및 제어

**MITRE ATT&CK 기술:** 암호화된 채널(T1573), 프록시(T1090)

**데이터 커넥터 원본:** 엔드포인트용 Microsoft Defender(이전에는 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 TOR 익명화 서비스로 아웃바운드 연결 요청이 수행된 후 Palo Alto Networks 방화벽이 비정상적인 인바운드 활동을 감지했음을 나타냅니다. 공격자가 네트워크 액세스 권한을 획득하고 본인이 수행하는 작업과 의도를 숨기려고 시도 중일 가능성이 높다는 것을 나타냅니다. 이 패턴을 따르는 TOR 네트워크 연결은 맬웨어 명령 및 제어 활동, 추가 맬웨어 다운로드 요청 또는 공격자가 원격 대화형 액세스를 설정하려 한다는 것을 의미할 수 있습니다. Palo Alto 로그에서 Azure Sentinel은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용되면 트래픽이 의심스러운 것으로 간주됩니다(의심스러운 데이터, 파일, 홍수, 패킷, 검사, 스파이웨어, URL, 바이러스, 취약성, wildfire-바이러스, wildfires). 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열된 [위협/콘텐츠 형식](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)에 해당하는 Palo Alto 위협 로그를 참조하세요.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>무단 액세스 시도 기록이 있는 IP에 대한 아웃바운드 연결 후 Palo Alto Networks 방화벽이 비정상적인 트래픽에 플래그 지정
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 명령 및 제어

**MITRE ATT&CK 기술:** 해당 없음

**데이터 커넥터 원본:** 엔드포인트용 Microsoft Defender(이전에는 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 무단 액세스 시도 기록이 있는 IP 주소에 대한 아웃바운드 연결이 설정된 후 Palo Alto Networks 방화벽이 비정상적인 활동을 감지했음을 나타냅니다. 공격자가 네트워크 액세스 권한을 획득했을 가능성이 있음을 나타냅니다. 이 패턴을 따르는 연결 시도는 맬웨어 명령 및 제어 활동, 추가 맬웨어 다운로드 요청 또는 공격자가 원격 대화형 액세스를 설정하려 한다는 것을 의미할 수 있습니다. Palo Alto 로그에서 Azure Sentinel은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용되면 트래픽이 의심스러운 것으로 간주됩니다(의심스러운 데이터, 파일, 홍수, 패킷, 검사, 스파이웨어, URL, 바이러스, 취약성, wildfire-바이러스, wildfires). 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열된 [위협/콘텐츠 형식](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)에 해당하는 Palo Alto 위협 로그를 참조하세요.

## <a name="ransomware"></a>랜섬웨어

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>의심스러운 Azure AD 로그인 후 랜섬웨어 실행

**MITRE ATT&CK 전술:** 초기 액세스, 영향

**MITRE ATT&CK 기술:** 유효한 계정(T1078), 충격을 주기 위해 데이터 암호화(T1486)

**데이터 커넥터 원본:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**설명:** 이 유형의 Fusion 인시던트는 의심스러운 Azure AD 계정 로그인 후 랜섬웨어 공격을 의미하는 비정상적인 사용자 동작이 감지되었음을 나타냅니다. Fusion 인시던트 설명에 언급된 계정이 손상되었으며 데이터 소유자를 갈취하거나 데이터 소유자가 데이터에 액세스하지 못하게 할 목적으로 데이터를 암호화하는 데 사용되었음을 강하게 시사합니다. 랜섬웨어 실행 경고가 포함된 의심스러운 Azure AD 로그인 경고의 순열은 다음과 같습니다.  

- **비정상적 위치로 불가능한 이동 후 클라우드 앱의 랜섬웨어**

- **일반적이지 않은 위치에서 로그인 이벤트 발생 후 클라우드 앱의 랜섬웨어**

- **감염된 디바이스에서 로그인 이벤트 발생 후 클라우드 앱의 랜섬웨어**

- **익명 IP 주소에서 로그인 이벤트 발생 후 클라우드 앱의 랜섬웨어**

- **자격 증명이 유출된 사용자에서 로그인 이벤트 발생 후 클라우드 앱의 랜섬웨어**

## <a name="remote-exploitation"></a>원격 익스플로잇

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>의심스러운 공격 프레임워크 사용 후 Palo Alto Networks 방화벽이 비정상적인 트래픽에 플래그 지정
이 시나리오는 현재 **미리 보기** 입니다.

**MITRE ATT&CK 전술:** 초기 액세스, 실행, 수평 이동, 권한 상승

**MITRE ATT&CK 기술:** 공용 애플리케이션 악용(T1190), 클라이언트 실행 악용(T1203), 원격 서비스 악용(T1210), 권한 상승 악용(T1068)

**데이터 커넥터 원본:** 엔드포인트용 Microsoft Defender(이전에는 MDATP), Palo Alto Networks 

**설명:** 이 유형의 Fusion 인시던트는 Metasploit와 같은 공격 프레임워크를 사용할 때와 비슷하게 프로토콜을 일반적이지 않은 방식으로 사용되는 것을 감지한 후 Palo Alto Networks 방화벽이 의심스러운 인바운드 활동을 감지했음을 나타냅니다. 이는 공격자가 네트워크 리소스에 대한 액세스 권한을 얻기 위해 서비스를 악용했거나, 공격자가 이미 액세스 권한을 획득했으며 수평 이동 및/또는 권한 상승을 위해 가용 시스템/서비스를 악용하려고 시도 중이라는 초기 표시일 수 있습니다. Palo Alto 로그에서 Azure Sentinel은 [위협 로그](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)를 집중적으로 다루며, 위협이 허용되면 트래픽이 의심스러운 것으로 간주됩니다(의심스러운 데이터, 파일, 홍수, 패킷, 검사, 스파이웨어, URL, 바이러스, 취약성, wildfire-바이러스, wildfires). 추가 경고 세부 정보는 Fusion 인시던트 설명에 나열된 [위협/콘텐츠 형식](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)에 해당하는 Palo Alto 위협 로그를 참조하세요.

## <a name="next-steps"></a>다음 단계

고급 다단계 공격 감지에 대해 자세히 알아보았으므로, [Azure Sentinel 시작](quickstart-get-visibility.md) 빠른 시작을 통해 데이터 및 잠재적 위협에 대한 가시성을 얻는 방법을 알아볼 수 있습니다.

준비된 인시던트를 조사할 준비가 되었으면 [Azure Sentinel을 사용하여 인시던트 조사](tutorial-investigate-cases.md) 자습서를 살펴보세요.

