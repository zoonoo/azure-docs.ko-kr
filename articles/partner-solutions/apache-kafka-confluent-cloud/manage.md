---
title: Confluent Cloud 관리 - Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal의 Confluent Cloud 관리에 대해 설명합니다. Single Sign-On을 설정하고, Confluent 조직을 삭제하고, 지원을 받는 방법을 알아봅니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989122"
---
# <a name="manage-the-confluent-cloud-resource"></a>Confluent Cloud 리소스 관리

이 문서에서는 Azure의 Confluent Cloud용 Apache Kafka 인스턴스를 관리하는 방법을 설명합니다. SSO(Single Sign-On)를 설정하고 Confluent 조직을 삭제하고 지원 요청을 만드는 방법을 보여 줍니다.

## <a name="single-sign-on"></a>Single Sign-On

조직에 SSO를 구현하기 위해 테넌트 관리자는 갤러리 애플리케이션을 가져올 수 있습니다. 이 단계는 선택 사항입니다. 애플리케이션을 가져오는 방법에 대한 자세한 내용은 [빠른 시작: Azure AD(Azure Active Directory) 테넌트에 애플리케이션 추가](../../active-directory/manage-apps/add-application-portal.md)를 참조하세요. 테넌트 관리자가 애플리케이션을 가져올 때 Confluent Cloud 포털에 대한 액세스를 허용하도록 사용자가 명시적으로 동의할 필요가 없습니다.

SSO를 사용하도록 설정하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Confluent Cloud 리소스의 인스턴스에 대한 **개요** 로 이동합니다.
1. **Confluent Cloud에서 관리** 할 링크를 선택합니다.

   :::image type="content" source="media/sso-link.png" alt-text="Confluent 포털 Single Sign-On":::

1. 테넌트 관리자가 SSO 동의를 위해 갤러리 애플리케이션을 가져오지 못한 경우 사용 권한 및 동의를 부여합니다. 이 단계는 **Confluent Cloud에서 관리** 할 링크에 처음 액세스할 때만 필요합니다.
1. Confluent Cloud 포털에 Single Sign-On할 Azure AD 계정을 선택합니다.
1. 동의를 제공하면 Confluent Cloud 포털로 리디렉션됩니다.

## <a name="set-up-cluster"></a>클러스터 설정

클러스터를 설정하는 방법에 대한 자세한 내용은 [Confluent Cloud에서 클러스터 만들기 - Confluent 설명서](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)를 참조하세요.

## <a name="delete-confluent-organization"></a>Confluent 조직 삭제

Confluent Cloud 리소스가 더 이상 필요하지 않은 경우 Azure와 Confluent Cloud에서 리소스를 삭제합니다.

Azure에서 리소스를 삭제하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 리소스** 를 선택하고 Confluent Cloud 리소스의 **이름으로 필터링** 하거나 _Confluent 조직_ 의 **리소스 종류** 로 필터링합니다. 또는 Azure Portal에서 리소스 이름을 검색합니다.
1. 리소스의 **개요** 에서 **삭제** 를 선택합니다.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="리소스 삭제 아이콘":::

1. 삭제를 확인하려면 리소스 이름을 입력하고 **삭제** 를 선택합니다.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="리소스 삭제를 확인하기 위한 프롬프트":::

Confluent Cloud에서 리소스를 삭제하려면 [Confluent Cloud 환경 - Confluent 설명서](https://docs.confluent.io/current/cloud/using/environments.html)와 [Confluent Cloud 기본 사항 - Confluent 설명서](https://docs.confluent.io/current/cloud/using/cloud-basics.html)를 참조하세요.

클러스터와 클러스터의 모든 데이터가 영구적으로 삭제됩니다. 계약에 데이터 보존 절이 포함된 경우 Confluent는 [서비스 약관 - Confluent 설명서](https://www.confluent.io/confluent-cloud-tos)에 명시된 기간 동안 데이터를 유지합니다.

클러스터 삭제 시간까지 비례 배분된 사용량에 대한 요금이 청구됩니다. 클러스터가 영구적으로 삭제되면 Confluent에서 확인 메일을 보냅니다.

## <a name="get-support"></a>지원 받기

Confluent에 지원 요청을 제출하려면 아래와 같이 [Confluent 지원](https://support.confluent.io)에 문의하거나 포털을 통해 요청을 제출합니다.

> [!NOTE]
> 처음 사용하는 사용자의 경우 Confluent 지원 포털에 로그인하기 전에 암호를 다시 설정합니다. Confluent Cloud 계정이 없는 경우 `cloud-support@confluent.io`에 메일을 보내 추가 지원을 받으실 수 있습니다.

포털에서 Azure 도움말 및 지원을 통해 또는 Azure의 Confluent Cloud용 Apache Kafka 인스턴스에서 직접 요청을 제출할 수 있습니다.

Azure 도움말 및 지원을 통해 요청을 제출하려면 다음을 수행합니다.

1. **도움말 + 지원** 을 선택합니다.
1. **지원 요청 만들기** 를 선택합니다.
1. 양식에서 **문제 유형** 에 **기술** 을 선택합니다. 구독을 선택합니다. 서비스 목록에서 **Azure의 Confluent** 를 선택합니다.

    :::image type="content" source="media/support-request-help.png" alt-text="도움말에서 지원 요청 만들기":::

리소스에서 요청을 제출하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Confluent 조직을 선택합니다.
1. 화면 왼쪽의 메뉴에서 **새 지원 요청** 을 선택합니다.
1. 지원 요청을 만들려면 **Confluent 포털** 에 대한 링크를 선택합니다.

    :::image type="content" source="media/support-request.png" alt-text="인스턴스에서 지원 요청 만들기":::

## <a name="next-steps"></a>다음 단계

문제 해결에 도움이 필요하면 [Confluent Cloud 솔루션용 Apache Kafka 문제 해결](troubleshoot.md)을 참조하세요.
