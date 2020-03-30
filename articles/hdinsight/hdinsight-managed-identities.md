---
title: Azure HDInsight에서 관리되는 ID
description: Azure HDInsight에서 관리되는 ID의 구현에 대한 개요를 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206013"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight에서 관리되는 ID

관리되는 ID는 Azure Active Directory(Azure AD)에 등록된 ID로, 자격 증명은 Azure에서 관리합니다. 관리되는 ID를 사용하면 Azure AD에 서비스 보안 주체를 등록하거나 인증서와 같은 자격 증명을 유지할 필요가 없습니다.

관리되는 ID는 Azure HDInsight에서 Azure AD 도메인 서비스에 액세스하거나 필요할 때 Azure Data Lake Storage Gen2의 파일에 액세스하는 데 사용됩니다.

관리되는 ID에는 사용자 할당 및 시스템 할당의 두 가지 유형이 있습니다. Azure HDInsight는 사용자가 할당한 관리되는 ID만 지원합니다. HDInsight는 시스템에서 할당된 관리 ID를 지원하지 않습니다. 사용자 할당된 관리되는 ID는 독립 실행형 Azure 리소스로 만들어지며, 이 리소스는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 반대로 시스템 할당된 관리 ID는 Azure AD에서 생성된 다음 특정 Azure 서비스 인스턴스에서 자동으로 직접 활성화됩니다. 그런 다음 해당 시스템에서 할당된 관리 ID의 수명은 활성화된 서비스 인스턴스의 수명과 연결됩니다.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 관리 ID 구현

Azure HDInsight에서 관리되는 ID는 클러스터의 각 노드에 프로비전됩니다. 그러나 이러한 ID 구성 요소는 HDInsight 서비스에서만 사용할 수 있습니다. 현재 HDInsight 클러스터 노드에 설치된 관리 ID를 사용하여 액세스 토큰을 생성하는 지원되는 방법은 없습니다. 일부 Azure 서비스의 경우 관리되는 ID는 사용자 스스로 다른 Azure 서비스와 상호 작용하기 위한 액세스 토큰을 획득하는 데 사용할 수 있는 끝점으로 구현됩니다.

## <a name="create-a-managed-identity"></a>관리되는 ID 만들기

관리되는 ID는 다음 방법 중 한 가지로 만들 수 있습니다.

* [Azure 포털](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure 파워쉘](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 리소스 관리자](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

관리되는 ID를 구성하는 나머지 단계는 사용할 시나리오에 따라 다릅니다.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight에서 관리되는 ID 시나리오

관리되는 ID는 여러 시나리오에서 Azure HDInsight에서 사용됩니다. 자세한 설정 및 구성 지침은 관련 문서를 참조하십시오.

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [엔터프라이즈 보안 패키지](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [고객 관리형 키 디스크 암호화](disk-encryption.md)

## <a name="faq"></a>FAQ
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>클러스터 를 만든 후 관리되는 ID를 삭제하면 어떻게 되나요?
관리되는 ID가 필요할 때 클러스터에 문제가 실행됩니다. 현재 클러스터를 만든 후에는 관리 ID를 업데이트하거나 변경할 수 없습니다. 따라서 클러스터 런타임 중에 관리되는 ID가 삭제되지 않도록 하는 것이 좋습니다. 또는 클러스터를 다시 만들고 새 관리되는 ID를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)
