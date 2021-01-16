---
title: Confluent 클라우드 관리-Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal의 Confluent 클라우드 관리에 대해 설명 합니다. Single Sign-On를 설정 하 고, Confluent 조직을 삭제 하 고, 지원을 받는 방법
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 2d13c183f0b3891fa92b5e2a6534acbf8102e032
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253502"
---
# <a name="manage-the-confluent-cloud-resource"></a>Confluent 클라우드 리소스 관리

이 문서에서는 Azure에서 Confluent Cloud의 Apache Kafka 인스턴스를 관리 하는 방법을 설명 합니다. SSO (Single Sign-On)를 설정 하 고, Confluent 조직을 삭제 하 고, 지원 요청을 만드는 방법을 보여 줍니다.

## <a name="single-sign-on"></a>SSO(Single sign-on)

조직에 대해 SSO를 구현 하기 위해 테 넌 트 관리자는 갤러리 응용 프로그램을 가져올 수 있습니다. 이 단계는 선택 사항입니다. 응용 프로그램을 가져오는 방법에 대 한 자세한 내용은 [빠른 시작: Azure Active Directory (AZURE AD) 테 넌 트에 응용 프로그램 추가](../../active-directory/manage-apps/add-application-portal.md)를 참조 하세요. 테 넌 트 관리자는 응용 프로그램을 가져올 때 Confluent 클라우드 포털에 대 한 액세스를 허용 하도록 사용자가 명시적으로 동의할 필요가 없습니다.

SSO를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Confluent 클라우드 리소스의 인스턴스에 대 한 **개요** 로 이동 합니다.
1. **Confluent 클라우드에서 관리할** 링크를 선택 합니다.

   :::image type="content" source="media/sso-link.png" alt-text="Confluent 포털 Single Sign-On 합니다.":::

1. 테 넌 트 관리자가 SSO 동의를 위해 갤러리 응용 프로그램을 가져오지 못한 경우 사용 권한 및 동의를 부여 합니다. 이 단계는 **Confluent 클라우드에서 관리할** 링크에 처음 액세스할 때만 필요 합니다.
1. Confluent 클라우드 포털에 Single Sign-On Azure AD 계정을 선택 합니다.
1. 동의가 제공 된 후에는 Confluent 클라우드 포털로 리디렉션됩니다.

## <a name="set-up-cluster"></a>클러스터 설정

클러스터를 설정 하는 방법에 대 한 자세한 내용은 [Confluent 클라우드에서 클러스터 만들기-Confluent 설명서](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)를 참조 하세요.

## <a name="delete-confluent-organization"></a>Confluent 조직 삭제

Confluent 클라우드 리소스가 더 이상 필요 하지 않은 경우 Azure 및 Confluent Cloud에서 리소스를 삭제 합니다.

Azure에서 리소스를 삭제 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 리소스** 를 선택 하 고 Confluent 클라우드 리소스의 이름 또는 _Confluent 조직의_ **리소스 종류** 를 **기준으로 필터링** 합니다. 또는 Azure Portal에서 리소스 이름을 검색 합니다.
1. 리소스 **개요** 에서 **삭제** 를 선택 합니다.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="리소스 삭제 아이콘.":::

1. 삭제를 확인 하려면 리소스 이름을 입력 하 고 **삭제** 를 선택 합니다.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="리소스 삭제를 확인 하는 메시지를 표시 합니다.":::

Confluent 클라우드에서 리소스를 삭제 하려면 [Confluent 클라우드 환경-Confluent 설명서](https://docs.confluent.io/current/cloud/using/environments.html) 및 [Confluent cloud 기본 사항-Confluent 설명서](https://docs.confluent.io/current/cloud/using/cloud-basics.html)를 참조 하세요.

클러스터 및 클러스터의 모든 데이터가 영구적으로 삭제 됩니다. 계약에 데이터 보존 절이 포함 된 경우 Confluent는 [Confluent 설명서](https://www.confluent.io/confluent-cloud-tos)에 지정 된 기간 동안 데이터를 유지 합니다.

클러스터 삭제 시간 까지의 비례 사용에 대 한 요금이 청구 됩니다. 클러스터가 영구적으로 삭제 된 후 Confluent에서 전자 메일 확인을 보냅니다.

## <a name="get-support"></a>지원 받기

Confluent에 지원 요청을 제출 하려면 아래와 같이 [Confluent 지원](https://support.confluent.io) 에 문의 하거나 포털을 통해 요청을 제출 합니다.

> [!NOTE]
> 처음 사용자의 경우 Confluent 지원 포털에 로그인 하기 전에 암호를 다시 설정 합니다. Confluent Cloud를 사용 하는 계정이 없는 경우 추가 지원을 위해에 전자 메일을 보냅니다 `cloud-support@confluent.io` .

리소스에서 요청을 제출 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Confluent 조직을 선택 합니다.
1. 화면의 왼쪽에 있는 메뉴에서 **새 지원 요청** 을 선택 합니다.
1. 지원 요청을 만들려면 **Confluent 포털** 에 대 한 링크를 선택 합니다.

    :::image type="content" source="media/support-request.png" alt-text="지원 요청을 만듭니다.":::

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 도움말은 [Confluent 클라우드 솔루션에 대 한 Apache Kafka 문제 해결](troubleshoot.md)을 참조 하세요.
