---
title: Azure SQL Database のセキュリティの概要 | Microsoft Docs
description: Azure SQL Database と SQL Server のセキュリティについて説明します。クラウドとオンプレミス SQL Server の違いなどが含まれます。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 1a21ab5af8c0dc5fbbe2c1dbab8931bd903df4ff
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235990"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database のセキュリティ機能の概要

この記事では、Azure SQL Database を使用して、アプリケーションのデータ層をセキュリティで保護するための基本の概要を示します。 説明されているセキュリティ戦略では、下の図に示すように多層防御アプローチに従い、外側から内側に移動します。

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>ネットワークのセキュリティ

Microsoft Azure SQL Database では、クラウドおよびエンタープライズ アプリケーション用のリレーショナル データベース サービスを提供します。 顧客データを保護できるように、IP アドレスまたは Azure 仮想ネットワーク トラフィック元に基づいてアクセス権が明示的に付与されるまで、ファイアウォールによってデータベース サーバーへのネットワーク アクセスが遮断されます。

### <a name="ip-firewall-rules"></a>IP ファイアウォールの規則

IP ファイアウォール規則では、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。 詳細については、[Azure SQL Database および SQL Data Warehouse ファイアウォール規則の概要](sql-database-firewall-configure.md)に関するページを参照してください。

### <a name="virtual-network-firewall-rules"></a>仮想ネットワーク ファイアウォールの規則

[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、Azure バックボーンで仮想ネットワークの接続を拡張し、トラフィックの発信元である仮想ネットワーク サブネットを Azure SQL Database が識別できるようにします。 Azure SQL Database にトラフィックが到達できるようにするには、SQL の[サービス タグ](../virtual-network/security-overview.md)を使用して、ネットワーク セキュリティ グループを介したアウトバウンド トラフィックを許可します。

[仮想ネットワークの規則](sql-database-vnet-service-endpoint-rule-overview.md)を使用すると、Azure SQL Database は、仮想ネットワーク内の選択されたサブネットから送信される通信のみを受け入れることができます。

> [!NOTE]
> ファイアウォール規則でのアクセスの制御は、**マネージド インスタンス**には適用され*ません*。 必要なネットワーク構成について詳しくは、「[マネージド インスタンスへの接続](sql-database-managed-instance-connect-app.md)」を参照してください

## <a name="access-management"></a>アクセス管理

> [!IMPORTANT]
> Azure 内でのデータベースとデータベース サーバーの管理は、ポータル ユーザー アカウントのロール割り当てによって制御されます。 この記事の詳細については、「[Azure Portal でのロール ベースのアクセス制御](../role-based-access-control/overview.md)」を参照してください。

### <a name="authentication"></a>Authentication

認証は、ユーザーが本人の主張どおりの人物であることを証明するプロセスです。 Azure SQL Database では、次の 2 種類の認証がサポートされます。

- **SQL 認証**:

    SQL データベース認証とは、ユーザーがユーザー名とパスワードを使用して [Azure SQL Database](sql-database-technical-overview.md) に接続するときに、そのユーザーを認証することです。 データベース用のデータベース サーバーの作成時に、ユーザー名とパスワードでの "サーバー管理者" ログインを指定する必要があります。 これらの資格情報を使用することで、"サーバー管理者" はデータベース所有者として、そのデータベース サーバーにある任意のデータベースを認証できます。 その後、サーバー管理者は、追加の SQL ログインとユーザーを作成できます。これにより、ユーザーはユーザー名とパスワードを使用して接続できるようになります。

- **Azure Active Directory 認証**:

    Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して [Azure SQL Database](sql-database-technical-overview.md) と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するメカニズムです。 Azure AD 認証では、管理者は、データベース ユーザーの ID とアクセス許可を他の Microsoft サービスと共に一元管理できます。 これにはパスワード ストレージの最小化が含まれます。また、これにより一元化されたパスワードのローテーション ポリシーが有効になります。

     SQL Database で Azure AD 認証を使用するには、**Active Directory 管理者**と呼ばれるサーバー管理者を作成する必要があります。 詳細については、[Azure Active Directory 認証を使用した SQL Database への接続](sql-database-aad-authentication.md)に関するページを参照してください。 Azure AD 認証では、マネージド アカウントとフェデレーション アカウントの両方がサポートされます。 フェデレーション アカウントでは、Azure AD とフェデレーションされた顧客ドメインの Windows ユーザーとグループがサポートされます。

    使用できる追加の Azure AD 認証オプションには、[多要素認証](../active-directory/authentication/concept-mfa-howitworks.md)と[条件付きアクセス](sql-database-conditional-access.md)を含む、[SQL Server Management Studio 用の Active Directory ユニバーサル認証](sql-database-ssms-mfa-authentication.md)接続があります。

> [!IMPORTANT]
> Azure 内でのデータベースとサーバーの管理は、ポータル ユーザー アカウントのロール割り当てによって制御されます。 この記事の詳細については、「[Azure Portal でのロール ベースのアクセス制御](../role-based-access-control/overview.md)」を参照してください。 ファイアウォール規則でのアクセスの制御は、**マネージド インスタンス**には適用され*ません*。 必要なネットワーク構成について詳しくは、[マネージド インスタンスへの接続](sql-database-managed-instance-connect-app.md)に関する次の記事をご覧ください。

承認は、Azure SQL Database 内のユーザーに割り当てられるアクセス許可を指し、ユーザーが実行できる操作を決定するものです。 アクセス許可は、データベース レベルのアクセス許可を定義する[データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)にユーザー アカウントを追加するか、ユーザーに特定の[オブジェクト レベルのアクセス許可](/sql/relational-databases/security/permissions-database-engine)を付与することで、制御されます。 詳細については、[ログインとユーザー](sql-database-manage-logins.md)に関するページを参照してください

ベスト プラクティスとして、職務に必要な最小限の特権を持つロールにユーザーを追加します。 サーバー管理者アカウントは、広範なアクセス許可を持つ db_owner ロールのメンバーであり、ユーザーには慎重に付与する必要があります。 Azure SQL Database でアプリケーションを使用する場合は、アクセス許可が制限されている[アプリケーション ロール](/sql/relational-databases/security/authentication-access/application-roles)を使用します。 これにより、データベースに接続しているアプリケーションに、そのアプリケーションで必要な最小限の特権が付与されます。

### <a name="row-level-security"></a>行レベルのセキュリティ

行レベルのセキュリティを使用して、クエリを実行しているユーザーの特性 (グループのメンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。 詳細については、「[行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)」を参照してください。

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  この認証方法では、ユーザー名とパスワードを使用します。 

Azure SQL Database でのアクセス許可の概要については、[ログインとユーザー](sql-database-manage-logins.md#permissions)に関するページを参照してください

## <a name="threat-protection"></a>脅威の防止

SQL Database では、監査と脅威検出機能を提供することで、顧客データをセキュリティで保護します。

### <a name="sql-auditing-in-log-analytics-and-event-hubs"></a>Log Analytics と Event Hubs での SQL 監査

SQL Database Auditing はデータベース アクティビティを追跡し、顧客が所有する Azure ストレージ アカウントの監査ログにデータベース イベントを記録することによって、セキュリティ標準のコンプライアンスを維持できるようにします。 Auditing を使用することで、ユーザーは進行中のデータベース アクティビティを監視し、過去のアクティビティを分析および調査して、潜在的な脅威や疑わしい不正使用、およびセキュリティ違反を特定することができます。 詳細については、「[SQL Database 監査の使用](sql-database-auditing.md)」を参照してください。  

### <a name="threat-detection"></a>脅威の検出

脅威検出では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる動作および害を及ぼす可能性のある試みがないか監査ログを分析することで、監査を強化します。 SQL インジェクション攻撃、潜在的なデータ侵入、ブルート フォース パスワード攻撃など、疑わしいアクティビティまたは異常なアクセス パターンに対して、アラートが作成されます。 脅威検出アラートは、[Azure Security Center](https://azure.microsoft.com/services/security-center/) で表示されます。ここでは、疑わしいアクティビティの詳細が提供され、また、脅威を軽減するためのアクションと共に、より詳しい調査のためのレコメンデーションが示されます。 脅威検出の料金は、15 ドル/サーバー/月です。 最初の 60 日間は無料です。 詳細については、「[SQL Database 脅威の検出の概要](sql-database-threat-detection.md)」をご覧ください。

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

### <a name="transport-layer-security-tls-encryption-in-transit"></a>トランスポート層セキュリティ TLS (転送中の暗号化)

SQL Database では、[トランスポート層セキュリティ](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)を使用して、移動中のデータを暗号化することで、顧客データをセキュリティで保護します。

> [!IMPORTANT]
> Azure SQL Database では、すべての接続に対して常に暗号化 (SSL/TLS) が適用され、すべてのデータがデータベースとクライアントの間の "移動中" に暗号化されることが保証されます。 これは、接続文字列での **Encrypt** または **TrustServerCertificate** の設定に関係なく行われます。
>
> アプリケーションの接続文字列では、暗号化された接続を指定し、サーバー証明書を信頼 "_しない_" ようにします (ADO.NET ドライバーの場合、これは **Encrypt=True** および **TrustServerCertificate=False** です)。 これは、アプリケーションにサーバーの検証を強制し、暗号化を適用することにより、中間者攻撃からアプリケーションを保護するのに役立ちます。 Azure portal から接続文字列を取得する場合は、正しい設定になっています。
>
> TLS と接続の詳細については、[TLS に関する考慮事項](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)に関するセクションを参照してください。

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (保存時の暗号化)

[Azure SQL Database の Transparent Data Encryption (TDE)](transparent-data-encryption-azure-sql.md) ではセキュリティ層が追加されており、生ファイルまたはバックアップへの未承認またはオフライン アクセスから保存データを保護するのに役立ちます。 一般的なシナリオには、ディスク ドライブやバックアップ テープなどのハードウェアまたはメディアの、データセンターでの盗用や非セキュアな破棄が含まれます。 TDE では、アプリケーション開発者が既存のアプリケーションを変更する必要がない、AES 暗号化アルゴリズムを使用して、データベース全体を暗号化します。

Azure では、新しく作成されたすべての SQL データベースが既定で暗号化され、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。  証明書のメンテナンスとローテーションはサービスによって管理され、ユーザーによる入力は必要ありません。 暗号化キーの制御を望まれるお客様は、[Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) でキーを管理できます。

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault のキー管理

 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) の [Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) サポートを利用することで、お客様は、 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) (Azure のクラウドベースの外部キー管理システム) を使用して、キーの管理とローテーションの所有権を取得できます。 キー コンテナーへのデータベースのアクセスが取り消された場合、データベースを暗号化解除して、メモリに読み込むことはできません。 Azure Key Vault ではキーの一元管理プラットフォームを提供し、厳しく監視されたハードウェア セキュリティ モジュール (HSM) を利用します。また、キー管理とデータ管理の職務の分離を可能にすることで、セキュリティ コンプライアンス要件の遵守を支援します。

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (使用時の暗号化)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) は、特定のデータベース列に格納された機微なデータ (クレジット カード番号、国民識別番号、_知る必要性_ に基づくデータなど) がアクセスされないようにすることを目的とした機能です。 これには、データベース管理者や、管理タスクを実行するためにデータベースへのアクセスを許可されているものの、暗号化された列の特定のデータには業務上、アクセスする必要がない他の特権ユーザーが含まれます。 データは常に暗号化されます。つまり、暗号化されたデータは、暗号化キーにアクセスできるクライアント アプリケーションによって処理される場合にのみ、暗号化解除されます。  暗号化キーは SQL に公開されることはなく、[Windows 証明書ストア](sql-database-always-encrypted.md)または [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) に格納できます。

### <a name="masking"></a>マスク

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>動的データ マスク

SQL Database 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。 動的データ マスクは、Azure SQL Database 内で機密の可能性があるデータを自動的に検出し、アプリケーション層への影響を最小限に抑えながらそれらのフィールドをマスクするための具体的な推奨事項を提示します。 指定されたデータベース フィールドに対するクエリの結果セットに含まれる機密データを難読化しますが、データベース内のデータは変更しません。 詳細については、「[SQL Database の動的データ マスク](sql-database-dynamic-data-masking-get-started.md)」を参照してください。

#### <a name="static-data-masking"></a>静的データ マスク

[静的データ マスク](/sql/relational-databases/security/static-data-masking)は、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18.0 プレビュー 5 以降で使用できるクライアント側のツールです。  静的データ マスクでは、ユーザーは、選択された列のデータが完全にマスクされているデータベースのコピーを作成できます。 利用可能なマスク機能には、NULL マスク、単一値マスク、シャッフルおよびグループ シャッフル マスク、文字列合成マスクが含まれます。 データベースのマスクされたコピーでは、組織は、マスクされたコピーを共有することで、運用環境とテスト環境を分離できます。 機微なデータは十分に保護され、他のすべてのデータベース特性が保持されています。 データベースへのサード パーティ アクセスが必要な場合は、データベースのマスクをお勧めします。

## <a name="security-management"></a>セキュリティ管理

### <a name="vulnerability-assessment"></a>脆弱性評価

[脆弱性評価](sql-vulnerability-assessment.md)では、全体的なデータベース セキュリティを積極的に向上させる目的で、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 脆弱性評価 (VA) は、高度な SQL セキュリティ機能の統合パッケージである、Advanced Data Security (ADS) オファリングの一部です。 脆弱性評価は、SQL ADS ポータルを使って一元的にアクセスおよび管理できます。

### <a name="data-discovery--classification"></a>データの検出と分類

データの検出と分類 (現在プレビュー段階) には、データベース内の機密データの検出、分類、ラベル付け、および保護を行うための、Azure SQL Database に組み込まれた高度な機能が用意されています。 最も機微なデータ (ビジネス/金融、医療、個人データなど) の検出と分類は、組織の情報保護水準において極めて重要な役割を果たします。 これは、以下のケースのインフラストラクチャとして機能します。

- さまざまなセキュリティ シナリオ (機微なデータに対する異常なアクセスの監視 (監査) とアラートなど)。
- 非常に機微なデータを含むデータベースへのアクセスの制御と、セキュリティの強化。
- データのプライバシー基準および規制のコンプライアンス要件を満たす支援。

詳細については、「[データの検出と分類の概要](sql-database-data-discovery-and-classification.md)」を参照してください。

### <a name="compliance"></a>コンプライアンス

アプリケーションでさまざまなセキュリティ要件を満たすのに役立つ上記の機能以外にも、Azure SQL Database では定期的な監査に参加し、さまざまなコンプライアンス基準に認定されています。 詳細については、「[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)」をご覧ください。ここから最新の [SQL Database コンプライアンス証明書](https://www.microsoft.com/trustcenter/compliance/complianceofferings)の一覧を入手できます。

## <a name="next-steps"></a>次の手順

- SQL Database でのアクセス制御機能の使用については、[アクセス制御](sql-database-control-access.md)に関するページを参照してください。
- データベース監査については、[SQL Database の監査に関する記事](sql-database-auditing.md)をご覧ください。
- 脅威の検出については、「[SQL Database Threat Detection](sql-database-threat-detection.md)」(SQL Database の脅威の検出) をご覧ください。
