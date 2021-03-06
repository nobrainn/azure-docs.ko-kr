---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182706"
---
Azure App Service의 Mobile Apps 기능은 [Azure Notification Hubs]를 사용하여 푸시를 보내므로 모바일 앱에 대해 알림 허브가 구성됩니다.

1. [Azure Portal]에서 **App Services**로 이동한 다음, 앱 백 엔드를 선택합니다. **설정**에서 **푸시**를 선택합니다.
2. 앱에 알림 허브 리소스를 추가하려면 **연결**을 선택합니다. 허브를 만들거나 기존 허브에 연결할 수 있습니다.

    ![허브 구성](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

이제 알림 허브를 Mobile Apps 백 엔드 프로젝트에 연결했습니다. 나중에 디바이스에 푸시하는 PNS(플랫폼 알림 시스템)에 연결하도록 이 알림 허브를 구성합니다.

[Azure 포털]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
