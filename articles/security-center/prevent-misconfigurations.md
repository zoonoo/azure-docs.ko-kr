---
title: Azure Security Center에서 잘못 된 구성을 방지 하는 방법
description: 권장 사항 세부 정보 페이지에서 Security Center의 ' 적용 ' 및 ' 거부 ' 옵션을 사용 하는 방법에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558186"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>적용/거부 권장 사항을 사용하여 구성 오류 방지

보안 구성 오류는 보안 인시던트의 주요 원인입니다. 이제 Security Center에는 특정 추천 사항과 관련하여 새 리소스의 구성 오류를 *방지* 하는 데 도움이 되는 기능이 있습니다. 

이 기능을 통해 워크로드를 안전하게 유지하고 보안 점수를 안정화할 수 있습니다.

특정 추천 사항에 따라 보안 구성을 적용하는 방법은 다음 두 가지 모드로 제공됩니다.

- Azure Policy의 **거부** 효과를 사용하여 비정상 리소스가 만들어지는 것을 중지할 수 있습니다.
- **적용** 옵션을 사용하여 Azure Policy의 **DeployIfNotExist** 효과를 활용하고, 비준수 리소스를 만들 때 자동으로 수정할 수 있습니다.

이는 선택한 보안 권장 사항에 대 한 리소스 세부 정보 페이지의 맨 위에 있습니다 ( [거부/적용 옵션을 사용 하는 권장 사항](#recommendations-with-denyenforce-options)참조).

## <a name="prevent-resource-creation"></a>리소스 생성 방지

1. 새 리소스에서 충족 해야 하는 권장 사항을 열고 페이지 맨 위에서 **거부** 단추를 선택 합니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="거부 단추가 강조 표시 된 권장 사항 페이지":::

    구성 창이 열리고 범위 옵션이 나열 됩니다. 

1. 관련 구독 또는 관리 그룹을 선택 하 여 범위를 설정 합니다.

    > [!TIP]
    > 행의 끝에 있는 세 개의 점을 사용 하 여 단일 구독을 변경 하거나 확인란을 사용 하 여 여러 구독 또는 그룹을 선택한 후 **거부로 변경** 을 선택할 수 있습니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy 거부에 대 한 범위 설정":::


## <a name="enforce-a-secure-configuration"></a>보안 구성 적용

1. 새 리소스가 충족 하지 않는 경우 템플릿 배포를 배포할 권장 사항을 열고 페이지 맨 위에 있는 **적용** 단추를 선택 합니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="적용 단추가 강조 표시 된 권장 사항 페이지":::

    구성 창이 열리고 모든 정책 구성 옵션이 나타납니다. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="구성 옵션 적용":::

1. 범위, 할당 이름 및 기타 관련 옵션을 설정 합니다.

1. **검토 + 만들기** 를 선택합니다.

## <a name="recommendations-with-denyenforce-options"></a>거부/적용 옵션을 사용한 권장 사항

이러한 권장 사항은 **deny** 옵션과 함께 사용할 수 있습니다.

- 방화벽 및 가상 네트워크 구성을 사용하여 스토리지 계정 액세스를 제한해야 함
- Azure Cache for Redis는 가상 네트워크 내에 있어야 함
- Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함
- Azure Machine Learning 작업 영역은 CMK(고객 관리형 키)를 사용하여 암호화해야 함
- Azure Spring Cloud는 네트워크 주입을 사용해야 함
- Cognitive Services 계정은 CMK(고객 관리형 키)로 데이터 암호화를 사용하도록 설정해야 함
- 컨테이너 CPU 및 메모리 한도를 적용해야 함
- 신뢰할 수 있는 레지스트리의 컨테이너 이미지만 배포해야 함
- 컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함
- 권한 상승을 포함하는 컨테이너를 사용하지 않아야 함
- 중요한 호스트 네임스페이스를 공유하는 컨테이너를 사용하지 않아야 함
- 컨테이너는 허용되는 포트에서만 수신 대기해야 함
- 변경 불가능한(읽기 전용) 루트 파일 시스템을 컨테이너에 적용해야 함
- Key Vault 키에는 만료 날짜가 있어야 함
- Key Vault 비밀에는 만료 날짜가 있어야 함
- 키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함
- 키 자격 증명 모음에 일시 삭제를 사용하도록 설정해야 함
- 최소 권한 Linux 기능을 컨테이너에 적용해야 함
- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.
- 컨테이너 AppArmor 프로필의 재정의 또는 비활성화를 제한해야 함
- 권한 있는 컨테이너를 피해야 함
- 컨테이너를 루트 사용자로 실행하지 않아야 함
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- Service Fabric 클러스터의 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 합니다.
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.
- 서비스는 허용되는 포트에서만 수신 대기해야 함
- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- 스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함
- 호스트 네트워킹 및 포트 사용을 제한해야 함
- 손상된 컨테이너에서 노드 액세스를 제한하려면 Pod HostPath 볼륨 탑재 사용을 알려진 목록으로 제한해야 함
- Azure Key Vault에 저장된 인증서의 유효 기간이 12개월을 넘어서는 안 됩니다.
- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함
- Azure Front Door Service 서비스에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함

이러한 권장 사항은 **적용** 옵션과 함께 사용할 수 있습니다.

- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.
- SQL 서버에서 SQL용 Azure Defender를 사용하도록 설정해야 함
- Azure Stream Analytics에서 진단 로그를 사용하도록 설정
- 배치 계정의 진단 로그를 사용하도록 설정해야 합니다.
- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.
- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.
- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.
- Logic Apps의 진단 로그를 사용하도록 설정해야 함
- Search Service의 진단 로그를 사용하도록 설정해야 함
- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.