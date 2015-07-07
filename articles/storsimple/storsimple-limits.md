<properties 
   pageTitle="StorSimple システムの制限"
   description="StorSimple コンポーネントに関するシステム制限、推奨サイズ、および接続について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# StorSimple システムの制限

## 概要

StorSimple は、データセンターのスケーラブルかつフレキシブルなストレージを提供します。ただし、StorSimple ソリューションを計画、展開、および実施する際に留意する必要がある制限がいくつかあります。StorSimple ソリューションを最大限に活用できるように、これらの制限と推奨事項のいくつかを以下の表に示します。

| 制限の種類 | 制限 | 説明 |
|----------------- | ------|--------- |
| ストレージ アカウントの資格情報の最大数 | 64 | |
| ボリューム コンテナーの最大数 | 64 | |
| ボリュームの最大数 | 255 | |
| 帯域幅テンプレートの最大数 | 25 | |
| 帯域幅テンプレートごとのスケジュールの最大数 | 168 | 毎日の 1 時間ごとのスケジュール (1 日 24 時間週 7 日)。 |
| ボリュームの最大サイズ | 64 TB | NTFS ボリュームの推奨サイズは 64 TB です。 |
| iSCSI 接続の最大数 | 512 | |
| イニシエーターからの iSCSI 接続の最大数 | 512 | |
| アクセス制御レコードのデバイスあたりの最大数 | 64 | |
| バックアップ ポリシーあたりのボリュームの最大数 | 24 | |
| 保持されるバックアップのバックアップ ポリシーあたりの最大数 | 64 | |
| バックアップ ポリシーあたりのスケジュールの最大数 | 10 | |
| 種類に関係なく保持できるスナップショットのボリュームあたりの最大数 | 256 | これには、ローカルのスナップショットとクラウドのスナップショットが含まれます。 |
| デバイス内に存在できるスナップショットの最大数 | 10,000 | |
| バックアップ、復元、または複製のために同時に処理できるボリュームの最大数 | 16 |<ul><li>ボリュームの数が 16 を超える場合は、処理スロットが使用可能になるのに合わせて順番に処理されます。</li><li>複製または復元されたボリュームの新しいバックアップは、操作が終了するまで実行できません。</li></ul>|
| 復元および複製時の復旧時間 | 2 分未満 | <ul><li>ボリュームは、そのサイズに関係なく、復元または複製操作から 2 分以内に使用可能になります。</li><li>最初は、データやメタデータの大部分がまだクラウドにあるため、ボリュームのパフォーマンスが通常より低下する場合があります。データがクラウドから StorSimple デバイスに流れるにつれてパフォーマンスが向上することがあります。</li><li>メタデータのダウンロードにかかる合計時間は、割り当てられたボリューム サイズによって異なります。割り当てられたボリューム データの TB あたり 5 分の速度で、メタデータはバックグラウンドでデバイスに自動的に移されます。この速度はクラウドへのインターネットの帯域幅によって影響を受けることがあります。</li><li>すべてのメタデータがデバイスに移動した時点で復元または複製操作は完了します。</li><li>復元または複製操作が完全に完了するまではバックアップ操作は実行できません。|
| シン復元の可用性 | 最後のフェールオーバー | |
| クライアントによる最大読み取り/書き込みスループット (SSD 層から提供された場合)* | 920/720 MB/秒 (単一の 10GbE ネットワーク インターフェイスを使用) | MPIO と 2 つのネットワーク インターフェイスを使用する場合は最大 2x。 |
| クライアントによる最大読み取り/書き込みスループット (HDD 層から提供された場合)* | 120/250 MB/秒 |
| クライアントによる最大読み取り/書き込みスループット (クラウド層から提供された場合)* | 11/41 MB/秒 | 読み取りスループットは、十分な I/O キューの深さを生成および維持するクライアントに依存します。 |

* I/O の種類ごとの最大スループットは、読み取り率が 100% で書き込み率が 100% の場合のシナリオにおいて測定されたものです。実際のスループットはこれより低くなる可能性があり、I/O の組み合わせとネットワーク状態によって異なります。

## 次のステップ

[StorSimple のシステム要件](storsimple-system-requirements.md)を確認します。

<!---HONumber=62-->