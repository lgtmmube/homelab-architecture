# Homelab

[![ホームラボの物理構成](physical-topology.png)](physical-topology.png)

[![クラスタとオブザーバビリティ基盤の構成](kube-obs.png)](kube-obs.png)

上は機器とネットワークの物理構成、下は観測データの収集・保存・可視化を中心とした構成図です。

## このラボについて

Kubernetesを中心に、アプリケーションの配布、ネットワーク、分散ストレージ、オブザーバビリティを構築した自宅ラボです。ラックサーバー、ミニPC、GPUノード、Raspberry Piを組み合わせて運用しています。

アプリケーションや基盤の状態を観測しながら、意図的な負荷をかけて実験しています。

## 主な構成

| 分野 | 役割 |
| --- | --- |
| クラスタ・通信 | Kubernetes(kubeadm)を中心に、Ciliumでクラスタ内の通信を制御。CoreDNSとkube-vipが名前解決とAPIへの接続を支える |
| 構成管理 | Ansibleでホストを準備し、Helm・Kustomizeで定義した構成をArgo CDで同期 |
| ストレージ | RookでCephを管理し、ブロックストレージとS3互換のオブジェクトストレージを提供 |
| 証明書・秘密情報 | cert-managerで証明書を管理し、Sealed Secretsで暗号化した秘密情報をGitで扱う |
| 通信・実行の観測 | Hubbleの通信ログ、対象アプリのTetragonイベント、Kubernetes監査ログで挙動を追う |

## オブザーバビリティ

メトリクス・ログ・トレース・プロファイルを収集し、Grafanaから各バックエンドを検索・表示します。

| 観測データ | 主な経路 | 見ていること |
| --- | --- | --- |
| メトリクス | Prometheus → Mimir | CPU・メモリ・ディスク・GPUの状態、クラスタやストレージの健全性 |
| ログ・イベント | Alloy → Loki | エラーの内容、状態変化、通信や実行の記録 |
| トレース | OpenTelemetry → Alloy → Tempo | リクエストの処理経路、時間がかかっている処理 |
| プロファイル | Go pprof → Alloy → Pyroscope | アプリケーションのCPU時間やメモリの使われ方 |

普段はGrafanaで全体の状態を確認し、気になる変化があれば関連するログやトレース、プロファイルを調べます。障害と復旧はAlertmanagerからSlackへ通知します。

## 独立監視とバックアップ

クラスタ外のRaspberry Piでも、Blackbox exporter・Prometheus・Alertmanagerを動かしています。クラスタへの接続性とKubernetes APIを外側から確認し、DHT22でサーバールームの温度・湿度も計測します。クラスタ内の監視とは別の経路で異常を通知します。(スイッチ　ルーター落ちたら..(*^^*))

復旧に備え、etcdのスナップショット、Sealed Secretsの復号鍵、GrafanaのDB・設定を日次で暗号化し、非公開のGitHub Releasesへ保存しています。

---

図中の製品名・ロゴの権利は各権利者に帰属します。[systemdロゴ](https://brand.systemd.io/)：Tobias Bernard（2019）、[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)。
