---
title: Azure 방화벽 프리미엄 미리 보기에 대 한 엔터프라이즈 CA 인증서 배포 및 구성
description: Azure Firewall Premium Preview에 대 한 엔터프라이즈 CA 인증서를 배포 하 고 구성 하는 방법을 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525498"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Azure 방화벽 미리 보기용 엔터프라이즈 CA 인증서 배포 및 구성

> [!IMPORTANT]
> Azure 방화벽 프리미엄은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


Azure 방화벽 프리미엄 미리 보기에는 인증서 인증 체인이 필요한 TLS 검사 기능이 포함 되어 있습니다. 프로덕션 배포의 경우 엔터프라이즈 PKI를 사용 하 여 Azure 방화벽 프리미엄에서 사용 하는 인증서를 생성 해야 합니다. 이 문서를 사용 하 여 Azure Firewall Premium Preview에 대 한 중간 CA 인증서를 만들고 관리 합니다.

Azure 방화벽 프리미엄 미리 보기에서 사용 하는 인증서에 대 한 자세한 내용은 [Azure 방화벽 프리미엄 미리 보기 인증서](premium-certificates.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

엔터프라이즈 CA를 사용 하 여 Azure 방화벽 프리미엄 미리 보기에서 사용할 인증서를 생성 하려면 다음 리소스가 있어야 합니다. 

- Active Directory 포리스트 
- 웹 등록을 사용 하는 Active Directory 인증 서비스 루트 CA 
- 프리미엄 계층 방화벽 정책을 사용 하는 Azure 방화벽 프리미엄 
- Azure Key Vault 
- Key Vault 액세스 정책에 정의 된 **인증서 및 암호** 에 대 한 읽기 권한이 있는 관리 id 

## <a name="request-and-export-a-certificate"></a>인증서 요청 및 내보내기

1. 일반적으로 루트 CA의 웹 등록 사이트에 액세스 `https://<servername>/certsrv` 하 고 **인증서 요청** 을 선택 합니다.
1. **고급 인증서 요청** 을 선택 합니다.
1. **이 CA에 요청을 만들어 제출** 합니다 .를 선택 합니다.
1. 다음과 같이 하위 인증 기관 템플릿을 사용 하 여 양식을 작성 합니다.
1. 요청을 제출 하 고 인증서를 설치 합니다.
1. Internet Explorer를 사용 하 여 Windows Server에서이 요청을 수행 한다고 가정 하 고 **인터넷 옵션** 을 엽니다.
1. **콘텐츠** 탭으로 이동 하 여 **인증서** 를 선택 합니다.
1. 방금 발급 한 인증서를 선택 하 고 **내보내기** 를 선택 합니다.
1. **다음** 을 선택 하 여 마법사를 시작 합니다. **예, 개인 키를 내보냅니다**.를 선택 하 고 **다음** 을 선택 합니다.
1. .pfx 파일 형식은 기본적으로 선택 됩니다. **가능 하면 인증 경로에 있는 인증서 모두 포함** 을 선택 취소 합니다. 전체 인증서 체인을 내보내는 경우 Azure 방화벽으로 가져오기 프로세스가 실패 합니다.
1. 키를 보호 하기 위해 암호를 할당 하 고 확인 한 후 **다음** 을 선택 합니다.
1. 파일 이름 및 내보내기 위치를 선택 하 고 **다음** 을 선택 합니다.
1. **마침** 을 선택 하 고 내보낸 인증서를 안전한 위치로 이동 합니다.

## <a name="add-the-certificate-to-a-firewall-policy"></a>방화벽 정책에 인증서 추가

1. Azure Portal에서 Key Vault의 인증서 페이지로 이동 하 고 **생성/가져오기** 를 선택 합니다.
1. 만들기 방법으로 **가져오기** 를 선택 하 고, 인증서 이름을로 지정한 후, 내보낸 .pfx 파일을 선택 하 고, 암호를 입력 한 다음, **만들기** 를 선택 합니다.
1. 방화벽 정책의 **TLS 검사 (미리 보기)** 페이지로 이동 하 고 관리 되는 id, Key Vault 및 인증서를 선택 합니다. 
1. **저장** 을 선택합니다.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS 조사":::

## <a name="validate-tls-inspection"></a>TLS 검사 유효성 검사

1. 대상 URL 또는 선택한 FQDN에 대 한 TLS 검사를 사용 하 여 응용 프로그램 규칙을 만듭니다.  예: `*bing.com`
1. 규칙의 원본 범위 내에 있는 도메인에 가입 된 컴퓨터에서 대상으로 이동 하 여 브라우저의 주소 표시줄 옆에 있는 잠금 기호를 선택 합니다. 인증서는 공용 CA가 아닌 엔터프라이즈 CA에서 발급 된 것으로 표시 해야 합니다.
1. 인증서 경로를 포함 하 여 자세한 정보를 표시 하는 인증서를 표시 합니다.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="인증서 세부 정보":::
1. Log Analytics에서 다음 KQL 쿼리를 실행 하 여 TLS 검사가 적용 된 모든 요청을 반환 합니다.
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   결과에는 검사 된 트래픽의 전체 URL이 표시 됩니다. :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL query":::

## <a name="next-steps"></a>다음 단계

[Azure Portal에서 Azure 방화벽 프리미엄 미리 보기](premium-portal.md)
