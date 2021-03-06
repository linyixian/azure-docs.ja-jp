---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742187"
---
アプリケーションへのサインインのみを可能にする場合は、**サインイン** ユーザー フローを使用します。 このユーザー フローは、サインイン中の顧客のエクスペリエンスと、サインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
**[管理]** にある **[ユーザー フロー]** を選択します。

ブレード上部の **[+ 新しいユーザー フロー]** をクリックします。

**[ユーザー フロー タイプを選択する]** の **[すべて]** を選択し、使用したい **[サインイン]** のバージョンを選択します。

**[名前]** によって、アプリケーションで使用されるサインイン ユーザー フロー名が決定されます。 たとえば、「**SiIn**」と入力します。

**[ID プロバイダー]** でオプションを選択します。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 Click **OK**.

**[アプリケーション要求]** の **[さらに表示する]** をクリックします。

**[要求を返す]** 列で、サインイン エクスペリエンスの成功後にアプリケーションに戻されるトークンで返される要求を選択します。 たとえば、"**表示名**"、"**ID プロバイダー**"、"**郵便番号**"、および "**ユーザーのオブジェクト ID**" などを選択します。 Click **OK**.

**Create** をクリックしてください。 作成したユーザー フローが "**B2C_1_SiIn**" と表示されます (**B2C\_1\_** フラグメントが自動的に追加されます)。

**[ユーザー フローを実行します]** をクリックします。

**[アプリケーション]** ボックスの一覧から **[Contoso B2C app]\(Contoso B2C アプリ\)** を、**[応答 URL]** ボックスの一覧から `https://localhost:44321/` を選択します。

**[ユーザー フローを実行します]** をクリックします。 新しいブラウザー タブが開き、アプリケーションへのサインインのコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ユーザー フロー の作成と更新が有効になるまで、最大で 1 分間かかります。
>