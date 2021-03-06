---
author: baanders
description: Azure Digital Twins 설정에서 가능한 추가 요구 사항에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 230304677b78f00b2d1288c846f8bf704cd8a497
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407472"
---
조직에서 구독 소유자의 추가 작업을 수행 하 여 앱 등록을 성공적으로 설정 하 고, 따라서 사용 가능한 Azure 디지털 쌍 인스턴스 설정을 완료할 수 있습니다. 필요한 단계는 조직의 특정 설정에 따라 달라질 수 있습니다.

소유자가 수행 해야 하는 몇 가지 일반적인 잠재적인 작업은 다음과 같습니다. 이러한 작업 및 기타 작업은 Azure Portal의 [*Azure AD 앱 등록*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 페이지에서 수행할 수 있습니다.
* 앱 등록에 대 한 관리자 동의를 부여 합니다. 조직에서 구독 내의 모든 앱 등록에 대해 Azure AD에서 전역으로 설정 해야 하는 *관리자 동의가* 있을 수 있습니다. 그렇다면 소유자는 앱 등록의 *API 권한* 페이지에서 회사에 대 한이 단추를 선택 하 여 앱 등록을 유효 하 게 설정 해야 합니다.

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="API 권한 아래의 ' 관리자 동의 허용 ' 단추의 포털 보기":::
  - 동의가 성공적으로 부여 된 경우 Azure Digital Twins의 항목에는 _ **(회사)** 에 대해 부여_ 된 *상태* 값이 표시 되어야 합니다.
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="API 권한에서 회사에 대해 부여 된 관리자 동의의 포털 보기":::
* 공용 클라이언트 액세스 활성화
* 웹 및 데스크톱 액세스에 대 한 특정 회신 Url 설정
* 암시적 OAuth2 인증 흐름 허용

앱 등록 및 다양 한 설치 옵션에 대 한 자세한 내용은 [*Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록*](https://docs.microsoft.com/graph/auth-register-app-v2)을 참조 하세요.

이제 Azure Digital Twins 인스턴스가 준비 되 고, 관리 권한을 할당 하 고, 클라이언트 앱에 액세스할 수 있는 권한을 설정할 수 있습니다.