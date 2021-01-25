---
title: Azure Portal을 사용하여 Azure Attestation 설정
description: Azure Portal을 사용하여 증명 공급자를 설정하고 구성하는 방법입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 52c0dd6825c8576d97e10d3f0568ca5fb81e789e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210808"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Attestation 설정

아래 단계에 따라 Azure Portal을 사용하여 증명 공급자를 관리합니다.

## <a name="attestation-provider"></a>증명 공급자

### <a name="create-an-attestation-provider"></a>증명 공급자 만들기

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>서명되지 않은 정책을 사용하여 공급자를 구성하려면

1.  Azure Portal 메뉴 또는 홈페이지에서 **리소스 만들기** 를 선택합니다.
2.  검색 상자에서 **attestation** 을 입력합니다.
3.  결과 목록에서 **Microsoft Azure Attestation** 을 선택합니다.
4.  Microsoft Azure Attestation 페이지에서 **만들기** 를 선택합니다.
5.  [증명 공급자 만들기] 페이지에서 다음 입력을 제공합니다.
    
    **구독**: 구독을 선택합니다.
    
    **리소스 그룹**: 기존 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 리소스 그룹 이름을 입력합니다.
    
    **이름**: 고유한 이름이 필요합니다.

    **위치**: 위치를 선택합니다. 
    
    **정책 서명자 인증서 파일**: 서명되지 않은 정책을 사용하여 공급자를 구성하려면 정책 서명자 인증서 파일을 업로드하지 않습니다. 
6.  필요한 입력이 제공되었으면 **검토 + 만들기** 를 클릭합니다.
7.  유효성 검사 문제가 있는 경우 수정하고, **만들기** 를 클릭합니다.

#### <a name="to-configure-the-provider-with-signed-policies"></a>서명된 정책을 사용하여 공급자를 구성하려면

1.  Azure Portal 메뉴 또는 홈페이지에서 **리소스 만들기** 를 선택합니다.
2.  검색 상자에서 **attestation** 을 입력합니다.
3.  결과 목록에서 **Microsoft Azure Attestation** 을 선택합니다.
4.  Microsoft Azure Attestation 페이지에서 **만들기** 를 선택합니다.
5.  [증명 공급자 만들기] 페이지에서 다음 정보를 제공합니다.
    
    a. **구독**: 구독을 선택합니다.
    
    b. **리소스 그룹**: 기존 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 리소스 그룹 이름을 입력합니다.
    
    다. **이름**: 고유한 이름이 필요합니다.

    d. **위치**: 위치를 선택합니다. 
    
    e. **정책 서명자 인증서 파일**: 정책 서명 인증서를 사용하여 증명 공급자를 구성하려면 인증서 파일을 업로드합니다. [여기](/azure/attestation/policy-signer-examples)에 있는 예제를 참조하세요. 
6.  필요한 입력이 제공되었으면 **검토 + 만들기** 를 클릭합니다.
7.  유효성 검사 문제가 있는 경우 수정하고, **만들기** 를 클릭합니다.

### <a name="view-attestation-provider"></a>증명 공급자 보기

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력하고 선택합니다.

### <a name="delete-attestation-provider"></a>증명 공급자 삭제

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  확인란을 선택하고, **삭제** 를 클릭합니다.
4.  '예'를 입력하고, **삭제** 를 클릭합니다. [또는]
1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  위쪽 메뉴에서 **삭제** 를 클릭하고, **예** 를 클릭합니다.


## <a name="attestation-policy-signers"></a>증명 정책 서명자

### <a name="view-policy-signer-certificates"></a>정책 서명자 인증서 보기

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  왼쪽 리소스 메뉴 또는 아래쪽 창에서 **정책 서명자 인증서** 를 클릭합니다.
5.  **정책 서명자 인증서 다운로드** 를 클릭합니다(정책 서명 요구 사항을 사용하지 않고 만든 증명 공급자에 대한 단추는 사용할 수 없음).
6.  다운로드한 텍스트 파일에는 모든 인증서가 JWS 형식으로 포함됩니다.
a.  다운로드한 인증서 수 및 인증서를 확인합니다.

### <a name="add-policy-signer-certificate"></a>정책 서명자 인증서 추가

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  왼쪽 리소스 메뉴 또는 아래쪽 창에서 **정책 서명자 인증서** 를 클릭합니다.
5.  위쪽 메뉴에서 **추가** 를 클릭합니다(정책 서명 요구 사항을 사용하지 않고 만든 증명 공급자에 대한 단추는 사용할 수 없음).
6.  정책 서명자 인증서 파일을 업로드하고, **추가** 를 클릭합니다. [여기](/azure/attestation/policy-signer-examples)에 있는 예제를 참조하세요.

### <a name="delete-policy-signer-certificate"></a>정책 서명자 인증서 삭제

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  왼쪽 리소스 메뉴 또는 아래쪽 창에서 **정책 서명자 인증서** 를 클릭합니다.
5.  위쪽 메뉴에서 **삭제** 를 클릭합니다(정책 서명 요구 사항을 사용하지 않고 만든 증명 공급자에 대한 단추는 사용할 수 없음).
6.  정책 서명자 인증서 파일을 업로드하고, **삭제** 를 클릭합니다. [여기](/azure/attestation/policy-signer-examples)에 있는 예제를 참조하세요. 

## <a name="attestation-policy"></a>증명 정책

### <a name="view-attestation-policy"></a>증명 정책 보기

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  왼쪽 리소스 메뉴 또는 아래쪽 창에서 **정책** 을 클릭합니다.
5.  기본 설정 **증명 유형** 을 선택하고, **현재 정책** 을 확인합니다.

### <a name="configure-attestation-policy"></a>증명 정책 구성

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>정책 서명 요구 사항을 사용하지 않고 증명 공급자를 만든 경우

##### <a name="upload-policy-in-jwt-format"></a>JWT 형식으로 정책 업로드

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  왼쪽 리소스 메뉴 또는 아래쪽 창에서 **정책** 을 클릭합니다.
5.  위쪽 메뉴에서 **구성** 을 클릭합니다.
6.  정책 서명 요구 사항을 사용하지 않고 증명 공급자를 만든 경우 사용자는 정책을 **JWT** 또는 **텍스트** 형식으로 업로드할 수 있습니다.
7.  **정책 형식** 을 **JWT** 로 선택합니다.
8.  정책 콘텐츠가 포함된 정책 파일을 **서명되지 않은/서명된 JWT** 형식으로 업로드하고, **저장** 을 클릭합니다. [여기](/azure/attestation/policy-examples)에 있는 예제를 참조하세요.
    
    파일 업로드 옵션의 경우 정책 미리 보기는 텍스트 형식으로 표시되며 이를 편집할 수 없습니다.

7.  위쪽 메뉴에서 **새로 고침** 을 클릭하여 구성된 정책을 확인합니다.

##### <a name="upload-policy-in-text-format"></a>텍스트 형식으로 정책 업로드

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  왼쪽 리소스 메뉴 또는 아래쪽 창에서 **정책** 을 클릭합니다.
5.  위쪽 메뉴에서 **구성** 을 클릭합니다.
6.  정책 서명 요구 사항을 사용하지 않고 증명 공급자를 만든 경우 사용자는 정책을 **JWT** 또는 **텍스트** 형식으로 업로드할 수 있습니다.
7.  **정책 형식** 을 **텍스트** 로 선택합니다.
8.  콘텐츠가 포함된 정책 파일을 **텍스트** 형식으로 업로드하거나, 텍스트 영역에서 정책 콘텐츠를 입력하고 **저장** 을 클릭합니다. [여기](/azure/attestation/policy-examples)에 있는 예제를 참조하세요.

    파일 업로드 옵션의 경우 정책 미리 보기는 텍스트 형식으로 표시되며 이를 편집할 수 없습니다.

8.  **새로 고침** 을 클릭하여 구성된 정책을 확인합니다.

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>정책 서명 요구 사항을 사용하여 증명 공급자를 만든 경우

##### <a name="upload-policy-in-jwt-format"></a>JWT 형식으로 정책 업로드

1.  Azure Portal 메뉴 또는 홈페이지에서 **모든 리소스** 를 선택합니다.
2.  필터 상자에서 증명 공급자 이름을 입력합니다.
3.  증명 공급자를 선택하고, 개요 페이지로 이동합니다.
4.  왼쪽 리소스 메뉴 또는 아래쪽 창에서 **정책** 을 클릭합니다.
5.  위쪽 메뉴에서 **구성** 을 클릭합니다.
6.  정책 서명 요구 사항을 사용하여 증명 공급자를 만든 경우 사용자는 정책을 **서명된 JWT 형식** 으로만 업로드할 수 있습니다.
7.  정책 파일을 **서명된 JWT 형식** 으로 업로드하고, **저장** 을 클릭합니다. [여기](/azure/attestation/policy-examples)에 있는 예제를 참조하세요.

    파일 업로드 옵션의 경우 정책 미리 보기는 텍스트 형식으로 표시되며 이를 편집할 수 없습니다.
    
8.  **새로 고침** 을 클릭하여 구성된 정책을 확인합니다.

 










