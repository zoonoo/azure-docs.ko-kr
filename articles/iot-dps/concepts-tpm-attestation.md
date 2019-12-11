---
title: Azure IoT Hub 장치 프로 비전 서비스-TPM 증명
description: 이 문서에서는 DPS (IoT 장치 프로 비전 서비스)를 사용 하는 TPM 증명 흐름에 대 한 개념적 개요를 제공 합니다.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975281"
---
# <a name="tpm-attestation"></a>TPM-igazolás

IoT Hub Device Provisioning Service는 지정 된 IoT Hub에 대 한 0 터치 장치 프로 비전을 구성 하는 데 사용 하는 IoT Hub에 대 한 도우미 서비스입니다. 장치 프로 비전 서비스를 사용 하 여 수백만 대의 장치를 안전한 방식으로 프로 비전 할 수 있습니다.

이 문서에서는 [TPM](./concepts-device.md)을 사용 하는 경우 id 증명 프로세스를 설명 합니다. TPM은 신뢰할 수 있는 플랫폼 모듈를 나타내며, HSM (하드웨어 보안 모듈) 유형입니다. 이 문서에서는 불연속, 펌웨어 또는 통합 TPM을 사용 하 고 있다고 가정 합니다. 소프트웨어 에뮬레이트된 Tpm은 프로토타입 또는 테스트에 적합 하지만 불연속, 펌웨어 또는 통합 된 Tpm과 동일한 수준의 보안을 제공 하지 않습니다. 프로덕션 환경에서는 소프트웨어 Tpm을 사용 하지 않는 것이 좋습니다. TPM의 유형에 대 한 자세한 내용은 [Tpm 소개](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf)를 참조 하세요.

이 문서는 HMAC 키 지원 및 해당 인증 키가 있는 TPM 2.0을 사용 하는 장치에만 해당 됩니다. 인증에 x.509 인증서를 사용 하는 장치에 대 한 것은 아닙니다. TPM은 TCG(신뢰할 수 있는 컴퓨팅 그룹)의 산업 전반에 걸친 ISO 표준 이며 [전체 tpm 2.0 사양](https://trustedcomputinggroup.org/tpm-library-specification/) 또는 [ISO/IEC 11889 사양](https://www.iso.org/standard/66510.html)에서 tpm에 대 한 자세한 내용을 확인할 수 있습니다. 또한이 문서에서는 공용 및 개인 키 쌍 및 암호화에 사용 되는 방법에 대해 잘 알고 있다고 가정 합니다.

장치 프로 비전 서비스 장치 Sdk는이 문서에 설명 된 모든 항목을 처리 합니다. 장치에서 Sdk를 사용 하는 경우 추가 항목을 구현할 필요가 없습니다. 이 문서를 통해 장치를 프로 비전 하 고 보안을 유지 하는 이유를 확인 하는 데 도움이 됩니다.

## <a name="overview"></a>Áttekintés

Tpm은 EK (인증 키) 라고 하는 항목을 신뢰의 보안 루트로 사용 합니다. EK는 TPM에 고유 하며 기본적으로 장치를 변경 하 여 장치를 새로운 장치로 변경 합니다.

Tpm에는 SRK (저장소 루트 키) 라고 하는 다른 유형의 키가 있습니다. Tpm의 소유권을 가져온 후 TPM의 소유자가 SRK를 생성할 수 있습니다. TPM 소유권 가져오기는 "누군가가 HSM에서 암호를 설정 합니다." 라고 하는 TPM 관련 방법입니다. TPM 장치가 새 소유자에 게 판매 되는 경우 새 소유자는 TPM의 소유권을 가져와 새 SRK를 생성할 수 있습니다. 새 SRK 생성은 이전 소유자가 TPM을 사용할 수 없도록 합니다. SRK는 TPM 소유자에 게 고유 하므로, 해당 소유자에 대해 TPM 자체에 데이터를 봉인 하는 데 SRK를 사용할 수 있습니다. SRK는 소유자가 키를 저장할 수 있는 샌드박스를 제공 하 고, 장치 또는 TPM이 판매 되는 경우 액세스 revocability 제공 합니다. 새 집으로 이동 하는 것과 같습니다. 소유권 가져오기는 도어에 대 한 잠금을 변경 하 고 이전 소유자 (SRK)가 남은 모든 가구를 삭제 하는 것입니다. 그러나 집 (EK) 주소를 변경할 수 없습니다.

장치를 설정 하 고 사용할 준비가 되 면 EK와 SRK를 모두 사용할 수 있게 됩니다.

![TPM의 소유권 가져오기](./media/concepts-tpm-attestation/tpm-ownership.png)

Tpm 소유권 가져오기에 대 한 참고 사항: tpm의 소유권 가져오기는 TPM 제조업체, 사용 되는 TPM 도구 집합 및 장치 OS를 비롯 한 다양 한 항목에 따라 달라 집니다. 사용자 시스템과 관련 된 지침에 따라 소유권을 가져옵니다.

장치 프로 비전 서비스는 EK (EK_pub)의 공개 부분을 사용 하 여 장치를 식별 하 고 등록 합니다. 장치 공급 업체는 제조 또는 최종 테스트 중에 EK_pub를 읽고 프로 비전 서비스에 EK_pub을 업로드 하 여 프로 비전에 연결할 때 장치가 인식 되도록 할 수 있습니다. 장치 프로 비전 서비스는 SRK 또는 소유자를 확인 하지 않으므로 TPM에서 고객 데이터를 삭제 하지만 EK 및 기타 공급 업체 데이터는 유지 되 고 장치 프로 비전 서비스에서 프로 비전에 연결할 때 장치를 계속 인식 합니다.

## <a name="detailed-attestation-process"></a>자세한 증명 프로세스

TPM이 있는 장치가 먼저 장치 프로 비전 서비스에 연결 하는 경우 서비스는 등록 목록에 저장 된 EK_pub에 대해 제공 된 EK_pub를 먼저 확인 합니다. EK_pubs 일치 하지 않으면 장치를 프로 비전 할 수 없습니다. EK_pubs 일치 하는 경우 서비스는 id를 증명 하는 데 사용 되는 안전한 챌린지 인 nonce 챌린지를 통해 EK의 개인 부분에 대 한 소유권을 증명 하는 장치를 요구 합니다. 장치 프로 비전 서비스는 nonce를 생성 한 다음,이를 SRK로 암호화 한 다음 EK_pub 하 여 초기 등록 호출 중에 장치에서 제공 됩니다. TPM은 항상 EK 보안의 비공개 부분을 유지 합니다. 이렇게 하면 위조가 방지 되 고 SAS 토큰이 권한 있는 장치에 안전 하 게 프로 비전 됩니다.

증명 프로세스를 자세히 살펴보겠습니다.

### <a name="device-requests-an-iot-hub-assignment"></a>장치에서 IoT Hub 할당을 요청 합니다.

먼저 장치가 장치 프로 비전 서비스 및 프로 비전 요청에 연결 합니다. 이렇게 하면 장치는 해당 등록 ID, ID 범위 및 EK_pub 및 TPM의 SRK_pub를 제공 합니다. 서비스는 암호화 된 nonce를 장치에 다시 전달 하 고, nonce의 암호를 해독 하 고, 다시 연결 하 고 프로 비전을 완료 하기 위해 SAS 토큰에 서명 하는 데 사용 합니다.

![장치 요청 프로 비전](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce 챌린지

장치는 nonce를 사용 하 고 EK 및 SRK의 비공개 부분을 사용 하 여 nonce를 TPM으로 해독 합니다. nonce 암호화의 순서는 변경 불가능 한 EK에서 SRK로 트러스트를 위임 합니다 .이는 새 소유자가 TPM의 소유권을 획득 하는 경우 변경 될 수 있습니다.

![Nonce 암호 해독](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Nonce의 유효성을 검사 하 고 자격 증명 받기

그러면 장치는 해독 된 nonce를 사용 하 여 SAS 토큰에 서명 하 고 서명 된 SAS 토큰을 사용 하 여 장치 프로 비전 서비스에 대 한 연결을 다시 설정할 수 있습니다. Nonce 챌린지를 완료 하면 서비스에서 장치를 프로 비전 할 수 있습니다.

![장치에서 장치 프로 비전 서비스에 연결 하 여 EK 소유권의 유효성을 검사 합니다.](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Következő lépések

이제 장치가 IoT Hub에 연결 되 고 장치의 키가 안전 하 게 저장 되었다는 정보를 안전 하 게 유지 합니다. 장치 프로 비전 서비스에서 TPM을 사용 하 여 장치의 id를 안전 하 게 확인 하는 방법을 배웠으므로 이제 자세한 내용을 보려면 다음 문서를 확인 하세요.

* [자동 프로 비전의 모든 개념에 대 한 자세한 정보](./concepts-auto-provisioning.md)
* Sdk를 사용 하 여 [자동 프로 비전을 시작 하 여](./quick-setup-auto-provision.md) 흐름을 처리 합니다.
