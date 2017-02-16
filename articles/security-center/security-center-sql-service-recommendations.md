---
title: "Azure Security Center에서 Azure SQL 서비스 및 데이터 보호 | Microsoft Docs"
description: "이 문서에서는 데이터 및 Azure SQL 서비스를 보호하고 보안 정책을 준수하는 데 도움이 되는 Azure Security Center의 권장 사항에 대해 설명합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7b5df25f46a2824acb361401ab90c960b8e5978f
ms.openlocfilehash: 8ce47dd649d1d945df506ed65c871cf3afe2f004


---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Azure Security Center에서 Azure SQL 서비스 및 데이터 보호
Azure 보안 센터에서는 Azure 리소스의 보안 상태를 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.  이러한 권장 사항은 가상 컴퓨터(VM), 네트워킹, SQL 및 데이터, 응용 프로그램 등의 Azure 리소스 유형에 적용됩니다.

이 문서에서는 Azure SQL 서비스 및 데이터에 적용되는 권장 사항에 대해 설명합니다. 권장 사항은 Azure SQL 서버 및 데이터베이스에 대한 감사 사용 및 SQL Database에 대한 암호화 설정, Azure Storage 계정의 암호화 설정에 초점을 둡니다.  아래 테이블을 참조로 사용하여 제공되는 SQL 서비스 및 데이터 권장 사항을 이해하고 각 권장 사항을 적용할 경우 어떻게 되는지 이해할 수 있습니다.

## <a name="available-sql-service-and-data-recommendations"></a>제공되는 SQL 서비스 및 데이터 권장 사항
| 권장 사항 | 설명 |
| --- | --- |
| [서버 SQL 감사 활성화](security-center-enable-auditing-on-sql-servers.md) |Azure SQL 서버(Azure SQL 서비스만 해당, 가상 컴퓨터에서 실행되는 SQL 제외됨)에 감사를 사용하는 것이 좋습니다. |
| [데이터베이스 SQL 감사 활성화](security-center-enable-auditing-on-sql-databases.md) |Azure SQL 데이터베이스(Azure SQL 서비스만 해당, 가상 컴퓨터에서 실행되는 SQL 제외됨)에 감사를 사용하는 것이 좋습니다. |
| [SQL 데이터베이스에서 투명한 데이터 암호화 활성화](security-center-enable-transparent-data-encryption.md) |SQL 데이터베이스(Azure SQL 서비스에만 해당)에 대해 암호화를 활성화하라는 권장 사항입니다. |
| [Azure Storage 계정에 암호화 사용](security-center-enable-encryption-for-storage-account.md) | 미사용 데이터에 대한 Azure Storage 서비스 암호화를 사용하도록 권장합니다. SSE(Storage 서비스 암호화)는 Azure Storage에 기록되는 데이터를 암호화하고 검색 전 해독하는 방식으로 작동합니다. SSE는 현재 Azure Blob service에만 사용할 수 있으며 블록 blob, 페이지 blob 및 추가 blob에 사용할 수 있습니다. 자세한 내용을 알아보려면 [미사용 데이터에 대한 Storage 서비스 암호화](../storage/storage-service-encryption.md)를 참조하세요.</br>SSE는 Resource Manager 저장소 계정에만 지원됩니다. 클래식 저장소 계정은 현재 지원되지 않습니다. 클래식 및 Resource Manager 배포 모델을 이해하려면 [Azure 배포 모델](../azure-classic-rm.md)을 참조하세요. |

## <a name="see-also"></a>참고 항목
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure 보안 센터에서 가상 컴퓨터 보호](security-center-virtual-machine-recommendations.md)
* [Azure 보안 센터에서 응용 프로그램 보호](security-center-application-recommendations.md)
* [Azure 보안 센터에서 네트워크 보호](security-center-network-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.



<!--HONumber=Jan17_HO1-->


