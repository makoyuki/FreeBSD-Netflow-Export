# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

FreeBSD 起動時に `ng_netflow(4)` を利用した Netflow エクスポータを自動起動するための rc.d スクリプト一式。
リポジトリの中身は以下のみ。

- `ng_netflow` — `/usr/local/etc/rc.d/` に配置して使う rc.d スクリプト本体（POSIX shell）
- `README.md` — 簡単な説明

## アーキテクチャ

`ng_netflow` は FreeBSD の `rc.subr` フレームワークに準拠した rc.d スクリプトで、`ngctl(8)` を使って netgraph ノードを構築する。

- `ng_netflow_prestart` — `ng_netflow_interface` と `ng_netflow_collect_addr` が設定されているか検証する
- `ng_netflow_start` — `ngctl -f-` にヒアドキュメントを渡し、以下の netgraph ノードを順に構築する
  1. 監視対象インターフェースに `netflow` ノード (`fnow_netflow`) を接続
  2. `fnow_netflow` に `ksocket` ノード (`fnow_nfsock`) を接続し、UDP で `ng_netflow_collect_addr:ng_netflow_collect_port` へエクスポート
- `ng_netflow_stop` — `netflow:` ノードを shutdown して後始末

設定は `/etc/rc.conf` 経由の rc.conf 変数（`ng_netflow_enable`, `ng_netflow_interface`, `ng_netflow_collect_addr`, `ng_netflow_collect_port`）で行う。デフォルトは `ng_netflow_enable="NO"`, `ng_netflow_collect_port="4444"`。

## 開発上の注意

- 本番の実行環境は FreeBSD のみ（`ngctl`, `rc.subr`, `netgraph` カーネルモジュールに依存）。Linux/macOS 上では動作確認ができないため、変更は shell スクリプトとしての静的な妥当性（構文、`rc.subr` の慣習）で確認する。
- ビルドや自動テストの仕組みはこのリポジトリには存在しない。構文チェックには `sh -n ng_netflow` を利用できる。
