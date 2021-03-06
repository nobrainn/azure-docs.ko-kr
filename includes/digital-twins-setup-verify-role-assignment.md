---
author: baanders
description: Azure Digital Twins 설정에서 역할 할당을 확인 하는 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405598"
---
역할 할당을 성공적으로 설정 했는지 확인 하는 한 가지 방법은 [Azure Portal](https://portal.azure.com)에서 Azure Digital twins 인스턴스에 대 한 역할 할당을 확인 하는 것입니다. [Azure Digital Twins 인스턴스의](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 포털 페이지로 이동 합니다 (이 링크를 사용 하거나 포털 검색 표시줄에서 검색할 수 있음). 그리고 확인 하려는 인스턴스의 이름을 선택 합니다. 

그런 다음 *액세스 제어 (IAM) > 역할 할당*에서 할당 된 모든 역할을 확인 합니다. 사용자는 *Azure 디지털 쌍 소유자 (미리 보기)* 의 역할을 사용 하 여 목록에 표시 되어야 합니다. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스에 대 한 역할 할당 보기":::