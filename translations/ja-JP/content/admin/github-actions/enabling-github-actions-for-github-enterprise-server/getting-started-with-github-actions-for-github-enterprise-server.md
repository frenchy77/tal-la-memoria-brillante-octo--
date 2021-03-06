---
title: GitHub Enterprise Server の GitHub Actions を使い始める
shortTitle: GitHub Actionsを使ってみる
intro: '{% data variables.product.prodname_ghe_server %} での {% data variables.product.prodname_actions %} の有効化と設定について初めて学びます。'
permissions: 'Site administrators can enable {% data variables.product.prodname_actions %} and configure enterprise settings.'
redirect_from:
  - /enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage
  - /admin/github-actions/enabling-github-actions-and-configuring-storage
  - /admin/github-actions/getting-started-with-github-actions-for-github-enterprise-server
versions:
  ghes: '*'
type: how_to
topics:
  - Actions
  - Enterprise
---

{% data reusables.actions.enterprise-beta %}

{% data reusables.actions.enterprise-github-hosted-runners %}

{% ifversion ghes > 2.22 %}

この記事では、サイト管理者が {% data variables.product.prodname_actions %} を使用するように {% data variables.product.prodname_ghe_server %} を設定する方法について説明しています。 ハードウェアとソフトウェアの要件、ストレージオプション、セキュリティ管理ポリシーについて説明します。

{% endif %}

## ハードウェアについての留意点を確認する

{% ifversion ghes = 2.22 or ghes = 3.0 %}

{% note %}

**注釈**: {% ifversion ghes = 2.22 %}{% data variables.product.prodname_actions %} は限定ベータとして {% data variables.product.prodname_ghe_server %} 2.22 で利用可能でした。 {% endif %}既存の {% data variables.product.prodname_ghe_server %} インスタンスを 3.0 以降にアップグレードしていて、{% data variables.product.prodname_actions %} を設定する場合は、ハードウェアの最小要件が増えていることに注意してください。 詳細は「[{% data variables.product.prodname_ghe_server %} をアップグレードする](/admin/enterprise-management/upgrading-github-enterprise-server#about-minimum-requirements-for-github-enterprise-server-30-and-later)」を参照してください。

{% endnote %}

{% endif %}

{%- ifversion ghes < 3.2 %}

{% data variables.product.product_location %} で使用可能な CPU およびメモリリソースによって、{% data variables.product.prodname_actions %} の最大ジョブスループットが決まります。

{% data variables.product.company_short %} での内部テストでは、さまざまな CPU およびメモリ設定の {% data variables.product.prodname_ghe_server %} インスタンスで次の最大スループットが実証されました。 インスタンスのアクティビティの全体的なレベルに応じて、スループットが異なる場合があります。

{%- endif %}

{%- ifversion ghes > 3.1 %}

The CPU and memory resources available to {% data variables.product.product_location %} determine the number of jobs that can be run concurrently without performance loss.

The peak quantity of concurrent jobs running without performance loss depends on such factors as job duration, artifact usage, number of repositories running Actions, and how much other work your instance is doing not related to Actions. Internal testing at GitHub demonstrated the following performance targets for GitHub Enterprise Server on a range of CPU and memory configurations:

{% endif %}

{%- ifversion ghes < 3.2 %}

| vCPUs | メモリ    | 最大ジョブスループット数 |
|:----- |:------ |:------------ |
| 4     | 32 GB  | デモまたは軽いテスト   |
| 8     | 64 GB  | 25ジョブ        |
| 16    | 160 GB | 35ジョブ        |
| 32    | 256 GB | 100ジョブ       |

{%- endif %}

{%- ifversion ghes > 3.1 %}

| vCPUs | メモリ    | Maximum Concurrency* |
|:----- |:------ |:-------------------- |
| 32    | 128 GB | 1500ジョブ              |
| 64    | 256 GB | 1900ジョブ              |
| 96    | 384 GB | 2200ジョブ              |

*Maximum concurrency was measured using multiple repositories, job duration of approximately 10 minutes, and 10 MB artifact uploads. You may experience different performance depending on the overall levels of activity on your instance.

{%- endif %}


既存のインスタンスのユーザに対して {% data variables.product.prodname_actions %} の{% ifversion ghes = 2.22 %}ベータを有効化した{% else %}有効化する{% endif %}場合は、ユーザのアクティビティのレベルとインスタンスの自動化を確認し、ユーザーに適切な CPU とメモリをプロビジョニングしたことを確認してください。 {% data variables.product.prodname_ghe_server %}のキャパシティとパフォーマンスのモニタリングに関する詳しい情報については「[アプラインアンスのモニタリング](/admin/enterprise-management/monitoring-your-appliance)」を参照してください。

{% data variables.product.product_location %} の最小ハードウェア要件の詳細については、インスタンスのプラットフォームのハードウェアに関する考慮事項を参照してください。

- [AWS](/admin/installation/installing-github-enterprise-server-on-aws#hardware-considerations)
- [Azure](/admin/installation/installing-github-enterprise-server-on-azure#hardware-considerations)
- [Google Cloud Plafform](/admin/installation/installing-github-enterprise-server-on-google-cloud-platform#hardware-considerations)
- [Hyper-V](/admin/installation/installing-github-enterprise-server-on-hyper-v#hardware-considerations)
- [OpenStack KVM](/admin/installation/installing-github-enterprise-server-on-openstack-kvm#hardware-considerations)
- [VMware](/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)
- [XenServer](/admin/installation/installing-github-enterprise-server-on-xenserver#hardware-considerations)

{% data reusables.enterprise_installation.about-adjusting-resources %}

## 外部ストレージの要件

{% data variables.product.prodname_ghe_server %} で {% data variables.product.prodname_actions %} を有効にするには、外部 Blob ストレージにアクセスできる必要があります。

{% data variables.product.prodname_actions %} は、blob ストレージを使用して、ワークフローログやユーザがアップロードしたビルドアーティファクトなど、ワークフロー実行によって生成されたアーティファクトを保存します。 必要なストレージ容量は、{% data variables.product.prodname_actions %} の使用状況によって異なります。 単一の外部ストレージ設定のみがサポートされており、複数のストレージプロバイダを同時に使用することはできません。

{% data variables.product.prodname_actions %} は、次のストレージプロバイダをサポートしています。

* Azure Blob ストレージ
* Amazon S3
* NAS ストレージ用の S3 対応 MinIO ゲートウェイ

{% note %}

**注釈:** これらは、{% data variables.product.company_short %} がサポートし、支援を提供できる唯一のストレージプロバイダです。 他の S3 API 互換のストレージプロバイダは、S3 API との違いにより、機能しない可能性があります。 追加のストレージプロバイダのサポートをリクエストするには、[お問い合わせ](https://support.github.com/contact)ください。

{% endnote %}

{% ifversion ghes = 2.22 %}

### Amazon S3 の権限

{% data reusables.actions.enterprise-s3-permission %}

## {% data variables.product.prodname_actions %} の有効化

{% data variables.product.prodname_ghe_server %} 2.22 での {% data variables.product.prodname_actions %} サポートは、限定ベータとして利用可能でした。 インスタンスの {% data variables.product.prodname_actions %} を設定するには、{% data variables.product.prodname_ghe_server %} 3.0 以降にアップグレードします。 詳しい情報については、[{% data variables.product.prodname_ghe_server %} 3.0 リリースノート](/enterprise-server@3.0/admin/release-notes)および「[{% data variables.product.prodname_ghe_server %} をアップグレードする](/admin/enterprise-management/upgrading-github-enterprise-server)」を参照してください。

## 参考リンク

- 「[{% data variables.product.prodname_ghe_server %} インスタンスを設定する](/enterprise/admin/installation/setting-up-a-github-enterprise-server-instance)」のプラットフォームに関する「ハードウェアの留意点」

{% endif %}

## Networking considerations

{% data reusables.actions.proxy-considerations %} For more information about using a proxy with {% data variables.product.prodname_ghe_server %}, see "[Configuring an outbound web proxy server](/admin/configuration/configuring-network-settings/configuring-an-outbound-web-proxy-server)."

{% ifversion ghes > 2.22 %}

## ストレージプロバイダで {% data variables.product.prodname_actions %} を有効化する

以下の手順のいずれかに従って、選択したストレージプロバイダで {% data variables.product.prodname_actions %} を有効にします。

* [Azure Blob ストレージで GitHub Actions を有効化する](/admin/github-actions/enabling-github-actions-with-azure-blob-storage)
* [Amazon S3 ストレージで GitHub Actions を有効化する](/admin/github-actions/enabling-github-actions-with-amazon-s3-storage)
* [NAS ストレージ用の MinIO ゲートウェイで GitHub Actions を有効化する](/admin/github-actions/enabling-github-actions-with-minio-gateway-for-nas-storage)

## Enterprise 内の {% data variables.product.prodname_actions %} のアクセス権限を管理する

ポリシーを使用して、{% data variables.product.prodname_actions %} へのアクセスを管理できます。 詳しい情報については、「[Enterprise に GitHub Actions のポリシーを施行する](/admin/github-actions/enforcing-github-actions-policies-for-your-enterprise)」を参照してください。

## セルフホストランナーの追加

{% data reusables.actions.enterprise-github-hosted-runners %}

{% data variables.product.prodname_actions %} ワークフローを実行するには、セルフホストランナーを追加する必要があります。 Enterprise、Organization、リポジトリレベルでセルフホストランナーを追加できます。 詳しい情報については「[セルフホストランナーの追加](/actions/hosting-your-own-runners/adding-self-hosted-runners)」を参照してください。

## Enterprise で使用できるアクションを管理する

ユーザーが Enterprise で使用できるアクションを制御できます。 これには、{% data variables.product.prodname_dotcom_the_website %} からのアクションへの自動アクセス用の {% data variables.product.prodname_github_connect %} の設定、または {% data variables.product.prodname_dotcom_the_website %} からのアクションの手動同期が含まれます。

詳しい情報については、「[Enterprise でのアクションの使用について](/admin/github-actions/about-using-actions-in-your-enterprise)」を参照してください。

## {% data variables.product.prodname_actions %} の一般的なセキュリティ強化

{% data variables.product.prodname_actions %} のセキュリティプラクティスについて詳しく学ぶには、「[{% data variables.product.prodname_actions %} のセキュリティ強化](/actions/learn-github-actions/security-hardening-for-github-actions)」を参照してください。

{% endif %}

## Reserved Names

When you enable {% data variables.product.prodname_actions %} for your enterprise, two organizations are created: `github` and `actions`. If your enterprise already uses the `github` organization name, `github-org` (or `github-github-org` if `github-org` is also in use) will be used instead. If your enterprise already uses the `actions` organization name, `github-actions` (or `github-actions-org` if `github-actions` is also in use) will be used instead. Once actions is enabled, you won't be able to use these names anymore.
