---
title: "Azure Data Lake Store의 암호화 | Microsoft Docs"
description: "Azure Data Lake Store에서 암호화 및 키 회전의 작동 방식 이해"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: ko-kr
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Azure Data Lake Store의 데이터 암호화

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Azure Data Lake Store의 암호화 개요

ADLS(Azure Data Lake Store) 암호화는 데이터를 보고하고 엔터프라이즈 보안 정책을 구현하고 규정 준수 요구 사항을 충족하는 기능을 제공합니다. 이 문서에서는 디자인 개요를 제공하고 Data Lake Store가 데이터 암호화를 구현하는 기술적인 측면을 설명합니다.

ADLS는 기본적으로 투명한 미사용 데이터 암호화를 지원합니다. 이러한 용어의 의미를 좀 더 자세히 알아보겠습니다.

* 기본적으로: 새 Azure Data Lake Store 계정을 만들 때 암호화를 사용하는 것이 기본 설정입니다. 따라서 Data Lake Store에 저장되는 데이터는 영구 미디어에 저장되기 전에 항상 암호화됩니다. 이것은 모든 데이터에 적용되는 동작이며 계정을 만든 후 변경할 수 없습니다.
* 투명한: ADLS는 영구 미디어에 저장하기 전에 자동으로 데이터를 암호화하고 검색하기 전에 데이터를 해독합니다. 암호화는 Data Lake Store 수준에서 관리자에 의해 구성 및 관리됩니다. 데이터 액세스 API가 변경되지 않으므로 암호화 때문에 Data Lake Store와 상호 작용하는 응용 프로그램 및 서비스를 변경할 필요가 없습니다.

전송되는 데이터(즉, 동작 중인 데이터)는 항상 Data Lake Store에서 암호화됩니다. 영구 미디어에 저장하기 전에 데이터를 암호화하는 것 외에도 HTTPS(HTTP over Secure Sockets Layer)를 사용하여 전송 중 또는 동작 중인 데이터를 항상 안전하게 보호합니다. HTTPS는 Data Lake Store REST 인터페이스에 지원되는 유일한 프로토콜입니다.

![그림 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Azure Data Lake Store 암호화 설정

Azure Data Lake Store 암호화는 계정을 만드는 동안 설정되며, 항상 기본적으로 사용됩니다. 고객은 키를 직접 관리할 수도 있고 Azure Data Lake Store에 키 관리를 맡길(기본값) 수도 있습니다.

Azure Data Lake Store 암호화를 설정하는 방법은 [시작](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)을 참조하세요.

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>내부 살펴보기 - Azure Data Lake Store에서 암호화가 작동하는 방식

### <a name="master-encryption-keys"></a>마스터 암호화 키

Azure Data Lake Store는 MEK(마스터 암호화 키)를 관리하는 두 가지 모드를 제공합니다. 마스터 암호화 키 사용에 대한 내용은 아래에 자세히 설명되어 있습니다. 지금은 마스터 암호화 키가 최상위 수준 키라고 가정하겠습니다. Data Lake Store에 저장된 데이터를 해독하려면 마스터 암호화 키 액세스 권한이 필요합니다.

마스터 암호화 키를 관리하기 두 가지 모드는 다음과 같습니다.

1.    서비스 관리 키
2.    고객 관리 키

두 모드에서 마스터 암호화 키는 Azure Key Vault에 저장하여 보호됩니다. Azure Key Vault는 Azure에서 완벽하게 관리되는 매우 안전한 서비스로 암호화 키를 보호하는 데 사용할 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [여기](https://azure.microsoft.com/services/key-vault)서 확인할 수 있습니다.

다음은 두 MEK 관리 모드에서 제공하는 기능을 간략히 비교한 것입니다.

|  | 서비스 관리 키 | 고객 관리 키 |
| --- | --- | --- |
|데이터가 어떻게 저장되나요?|저장하기 전에 항상 암호화|저장하기 전에 항상 암호화|
|마스터 암호화 키가 어디에 저장되나요?|Azure 키 자격 증명 모음|Azure 키 자격 증명 모음|
|Azure Key Vault 외부에 저장되는 암호화 키가 있나요?|아니요|아니요|
|Azure Key Vault에서 MEK를 검색할 수 있나요?|안 됩니다. 일단 Key Vault에 저장되면 암호화 및 암호 해독에만 사용할 수 있습니다.|안 됩니다. 일단 Key Vault에 저장되면 암호화 및 암호 해독에만 사용할 수 있습니다.|
|Azure Key Vault 및 MEK 소유자는 누구인가요?|Azure Data Lake Store 서비스.|고객은 자신의 Azure 구독에 속한 Azure Key Vault를 소유합니다. Key Vault의 MEK는 소프트웨어 또는 하드웨어(HSM)를 통해 관리할 수 있습니다.|
|고객이 Azure Data Lake Store 서비스의 MEK에 대한 액세스를 취소할 수 있나요?|아니요|예. 고객은 Azure Key Vault의 액세스 제어 목록을 관리하고 Azure Data Lake Store 서비스의 서비스 ID에 대한 액세스 제어 항목을 제거할 수 있습니다.|
|고객이 MEK를 영구적으로 삭제할 수 있나요?|아니요|예. 고객이 Azure Key Vault에서 MEK를 삭제하면 Azure Data Lake Store 서비스를 포함하여 그 누구도 ADLS 계정의 데이터를 해독할 수 없습니다. <br><br> 고객이 Azure Key Vault에서 MEK를 삭제하기 전에 명시적으로 백업하면 MEK를 복원하고 데이터 복구할 수 있습니다. 그러나 고객이 Azure Key Vault에서 MEK를 삭제하기 전에 백업하지 않으면 ADLS 계정의 데이터를 다시는 해독할 수 없습니다.|


MEK 및 MEK가 상주하는 Key Vault를 관리하는 사람이 다르다는 상위 수준의 차이점을 제외한 디자인의 나머지 부분은 두 모드가 동일합니다.

마스터 암호화 키에 대한 모드 선택과 관련하여 기억해야 할 몇 가지 중요한 사항이 있습니다.

1.    ADLS 계정을 프로비전할 때 고객 관리 키와 ADLS 관리 키 중에서 무엇을 사용할 것인지 선택할 수 있습니다.
2.    ADLS 계정을 프로비전한 후에는 모드를 변경할 수 없습니다.

### <a name="encryption-and-decryption-of-data"></a>데이터 암호화 및 암호 해독

Azure Data Lake의 데이터 암호화 디자인에 사용되는 세 가지 유형의 키가 있습니다. 다음 테이블에는 이러한 키의 작동 방식이 요약되어 있습니다.

| 키                   | 약어 | 연결 대상 | 저장 위치                             | 형식       | 참고 사항                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| 마스터 암호화 키 | MEK          | ADLS 계정 | Azure 키 자격 증명 모음                              | 비대칭 | ADLS 관리 또는 고객 관리                                                              |
| 데이터 암호화 키   | DEK          | ADLS 계정 | 영구 저장소 – ADLS 서비스에서 관리 | 대칭  | DEK는 MEK를 통해 암호화되며 암호화된 DEK는 서비스의 영구 미디어에 저장 |
| 블록 암호화 키  | BEK          | 데이터 블록 | 없음                                         | 대칭  | BEK는 DEK 및 데이터 블록에서 파생                                                      |

다음 다이어그램은 이러한 개념을 보여 줍니다.

![그림 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>파일 암호를 해독해야 할 때의 의사 알고리즘:
1.    ADLS 계정의 DEK가 캐시되었으며 사용 가능한지 확인합니다.
    * 그렇지 않으면 영구 저장소에서 암호화된 DEK를 읽고 Azure Key Vault로 보내서 해독합니다. 해독된 DEK를 메모리에 캐시하여 이제 사용할 수 있습니다.
2.    파일에 있는 모든 데이터 블록에 대해
    * 영구 저장소에서 암호화된 데이터 블록을 읽습니다.
    * DEK 및 암호화된 데이터 블록에서 BEK를 생성합니다.
    * BEK를 사용하여 데이터를 해독합니다.
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>데이터 블록을 암호화해야 할 때의 의사 알고리즘:
1.    ADLS 계정의 DEK가 캐시되었으며 사용 가능한지 확인합니다.
    * 그렇지 않으면 영구 저장소에서 암호화된 DEK를 읽고 Azure Key Vault로 보내서 해독합니다. 해독된 DEK를 메모리에 캐시하여 이제 사용할 수 있습니다.
2.    DEK에서 데이터 블록에 대한 고유의 BEK를 생성합니다.
3.    AES-256 암호화를 사용하여 BEK로 데이터 블록을 암호화합니다.
4.    암호화된 데이터 블록을 영구 저장소에 저장합니다.

> [!NOTE] 
> 성능상의 이유로, 위험하지 않은 DEK(데이터 암호화 키)는 짧은 기간 동안 메모리에 캐시되었다가 그 기간이 경과되면 즉시 삭제됩니다. 영구 미디어에는 항상 MEK(마스터 암호화 키)를 통해 암호화된 데이터가 저장됩니다.

## <a name="key-rotation"></a>키 회전

Azure Data Lake Store는 고객 관리 키를 사용할 경우 MEK(마스터 암호화 키) 회전이 가능합니다. 고객 관리 키를 사용하여 ADLS 계정을 설정하는 방법은 [시작](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) 페이지를 참조하세요.

### <a name="pre-requisites"></a>필수 조건

Azure Data Lake 계정을 설정할 때 고객이 자체 키를 사용하기로 선택했습니다. 계정을 만든 후에는 이 옵션을 변경할 수 없습니다. 암호화에 대한 기본 옵션을 사용하면 데이터는 항상 Azure Data Lake를 통해 관리되는 키를 사용하여 암호화되며, 이 옵션에서는 키가 Azure Data Lake를 통해 관리되므로 고객이 키를 회전할 수 없습니다. 아래 단계에서는 사용자가 고객 관리 키를 사용(Key Vault에서 사용자 고유의 키 선택)하는 것으로 가정합니다.

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Azure Data Lake Store에서 키(MEK)를 회전하는 방법

1. 새 [Azure Portal](https://portal.azure.com/)에 로그온합니다.
2. Azure Data Lake Store 계정에 연결된 키를 저장하는 Key Vault로 이동하여 키를 선택합니다.

    ![구성](./media/data-lake-store-encryption/keyvault.png)

3.    Azure Data Lake Store 계정에 연결된 키를 선택하고 이 키의 새 버전을 만듭니다.
  
   현재 Azure Data Lake는 새 버전의 키에 대한 회전만 지원하고 다른 키에 대한 회전은 지원하지 않습니다.

   ![새 버전](./media/data-lake-store-encryption/keynewversion.png)

4.    Azure Data Lake Storage 계정으로 이동하여 암호화를 선택합니다.

    ![새 버전](./media/data-lake-store-encryption/select-encryption.png)

5.    새 키 버전을 사용할 수 있다고 알려주는 주석과 이 새 버전으로 키를 회전하는 단추가 보일 것입니다. 키 회전을 클릭하여 키를 새 버전으로 업데이트합니다.

    ![완료](./media/data-lake-store-encryption/rotatekey.png)

6. 이 작업은 2분 이내에 완료되며 키 회전으로 인해 예상되는 가동 중지 시간은 없습니다. 작업이 완료되면 새 버전의 키가 사용됩니다.

