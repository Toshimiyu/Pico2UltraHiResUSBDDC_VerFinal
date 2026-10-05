# Pico2 UltraHires USB-DDC (USB Digital Audio Device)

This project is based on the original work by ArqAlice (MIT License).

Originally developed for the PICO_AUDIO_PACK environment (RP2040), this firmware has been fully restructured for Raspberry Pi Pico2 (RP2350).

RP2040-based boards are not supported.

GPIO assignments have been remapped to GP9 / GP10 / GP11 for compatibility with the original PICO_AUDIO_PACK hardware.

Additional modifications were implemented to improve audio quality, stability and DAC compatibility.

Operation has been verified on Pico Audio Pack (PCM5100).

------------------------------------------------------------------------------

■ 概要

本プロジェクトは、RP2350（Raspberry Pi Pico2）上で動作する USB Audio Class 1.0 準拠の USBデジタルオーディオコンバータ（USB-DDC）です。

PCなどのUSBホストから受信した2ch PCMオーディオ信号をリアルタイムでアップサンプリングし、I²S信号としてDACへ出力します。

DMA・PIO・マルチコア処理を活用することで、低遅延かつ高精度なオーディオ伝送を実現しています。

Pico Audio Pack向けにピンアサイン変更および音質最適化を実施しています。

------------------------------------------------------------------------------

■ 特長

・USB Audio Class 1.0 準拠
・OS標準ドライバで動作
・最大32倍アップサンプリング
・FIR / IIR ハイブリッドフィルタ
・DMA + PIO によるI²S出力
・RP2350マルチコア処理
・32bit I²S出力
・Pico Audio Pack対応

------------------------------------------------------------------------------
■ オリジナル版からの主な変更点

本プロジェクトは ArqAlice 氏によるオリジナル実装をベースとしているが、
RP2350環境およびPico Audio Pack向けに大幅な改良を実施している。

主な変更内容

・RP2350デュアルコア構成に合わせて処理構成を最適化

・Pico Audio Pack互換動作のためI²Sピン配置を変更
  I2S DATA : GP9
  I2S BCLK : GP10
  I2S LRCK : GP11

・Pico Audio Pack搭載PCM5100で動作確認

・DMAおよびI²S処理を調整し安定性を向上

・USBオーディオ動作の安定性を改善

・アップサンプリング処理の最適化

・ESS ES9038Q2M / ES9039Q2Mへの対応追加

・音質評価および実機検証に基づく調整を実施

・REWによる測定評価を実施し性能検証済み

------------------------------------------------------------------------------

■ 対応DAC

・TI PCM5102
・ESS ES9038Q2M
・ESS ES9039Q2M

※ PCM5100はPICO_AUDIO_PACK環境でのみ動作確認済み
※ Pico2直結でのPCM5100動作は未確認

------------------------------------------------------------------------------

■ USB入力仕様

Audio Class
USB Audio Class 1.0

Channels
2ch Stereo

Bit Depth
16bit / 24bit

Supported Sample Rates
44.1kHz
48kHz
88.2kHz
96kHz

------------------------------------------------------------------------------

■ I²S出力仕様

Format
I²S 32bit

Channels
2ch Stereo

Bit Depth
32bit Fixed

Maximum Sample Rate
1536kHz / 1411.2kHz

Supported Output Rates

1536kHz / 1411.2kHz
768kHz / 705.6kHz
384kHz / 352.8kHz
192kHz / 176.4kHz

------------------------------------------------------------------------------

■ システム構成

RP2350 (Raspberry Pi Pico2)

DMA + PIO I²S Engine

Dual Core Processing

Core0
・USB Audio Processing
・8x FIR Oversampling

Core1
・2x FIR Oversampling
・4x BiQuad IIR Oversampling
・DMA Processing
・I²S Transmission

USB Stack

LUFA-based USB Audio Class Implementation

Timing Control

Timer Interrupt + Buffer Feedback Control

------------------------------------------------------------------------------

■ ビルド方法

1. Visual Studio Codeをインストール

2. Raspberry Pi Pico Extensionをインストール

3. 本リポジトリをクローン

4. Build実行

5. Pico2をBOOTSEL押下状態で接続

6. 生成されたUF2を書き込み

7. USB Audio Deviceとして認識

------------------------------------------------------------------------------

■ コンフィグレーション

src/common.h の User Configurable セクションから変更可能

・ピンアサイン
・アップサンプリング倍率
・DAC種類

------------------------------------------------------------------------------

■ Pico Audio Pack互換ピン配置

I2S DATA : GP9
I2S BCLK : GP10
I2S LRCK : GP11

I2C SDA : GP6
I2C SCL : GP7

DAC ENABLE : GP5
POWERMODE SW : GP0

------------------------------------------------------------------------------

■ アップサンプリング設定

1536kHz / 1411.2kHz
8x FIR + 4x IIR

768kHz / 705.6kHz
8x FIR + 2x FIR

384kHz / 352.8kHz
8x FIR

192kHz / 176.4kHz
4x FIR

------------------------------------------------------------------------------

■ ESS DAC設定

USE_ESS_DAC = true

KIND_ESS_DAC

・ES9038Q2M
・ES9039Q2M

※ ES9039Q2M は 1536kHz / 1411.2kHz 非対応

------------------------------------------------------------------------------
■ 測定対象について

本測定結果は、標準状態の Pico2 および Pico Audio Pack ではなく、
実際に運用しているモディファイ済み構成に対して実施されたものである。

測定時の構成

・Pico2 UltraHires USB-DDC（RP2350）
・Pico Audio Pack（PCM5100）
・実使用環境での最終構成

主なハードウェア改修履歴

Ver1

・Pico2側 VSYS-GND間へ
  X7R 10μF + C0G 0.1μF追加

・Pico Audio Pack側 LDO入力部へ
  X7R 10μF + C0G 0.1μF追加

・LPF定数変更
  C2200pF → 1200pF

Ver2

・Pico Audio Pack側 LDO入力部へ
  PLMCAP 1μF追加

・AVDD-GND間へ
  C0G 0.1μF追加

・CVDD-GND間へ
  C0G 0.1μF追加

Ver3

・Pico Audio Pack側 AVDD-GND間へ
  PLMCAP 3.3μF追加

上記改修は、

・電源インピーダンス低減
・デカップリング強化
・DACアナログ電源安定化
・LPF定数最適化
・過渡応答特性改善

を目的として実施した。

したがって、本測定結果は標準状態の Pico Audio Pack や
オリジナルファームウェアの性能を示すものではなく、

本リポジトリに収録されたファームウェアと、
上記改修を含む実際の運用システム全体に対する評価結果である。

------------------------------------------------------------------------------
■ 測定結果の位置付け

本測定結果は、本リポジトリに収録されているファームウェアと、
Pico Audio Packに対して実施したハードウェア改修を組み合わせた
最終運用構成における評価結果である。

したがって、同一ファームウェアを使用した場合でも、
DAC構成、実装状態、使用部品、電源構成、
およびハードウェア改修内容の違いにより、
同一の測定結果を保証するものではない。

------------------------------------------------------------------------------

※ 本測定結果は「Ver3」改修後の最終構成で取得したものである。

■ REW測定結果サマリー

測定日

2026/09/26

測定条件

96kHz
512k FFT
Hann Window

測定環境

Sound Blaster HD改（SB-1240改）

主要測定結果

THD

100Hz  : 0.0056%
1kHz   : 0.0040%
10kHz  : 0.012%
20kHz  : 0.013%

IMD

DIN    : 0.016%
SMPTE  : 0.020%
CCIF   : 0.016%

TDFD

Bass   : 0.0014%
Phono  : 0.00083%

時間軸特性

・Flat Group Delay
・Clean Impulse Response
・No Waterfall Resonance
・No Significant Spectrogram Artifacts
・Excellent 50Hz Square-Wave Reproduction

------------------------------------------------------------------------------

■ 技術評価要約

REW測定結果は、SB-1240改の既知ベースライン性能との比較によって評価された。

評価結果より、

・高域THDおよびCCIFは測定系が先に限界へ到達している可能性が高い

・時間軸特性は極めて良好

・多信号時の線形性は優秀

・顕著な位相乱れ、共振、過渡応答異常は確認されない

ことが確認された。

------------------------------------------------------------------------------

■ ライセンス

本プロジェクトは MIT License のもとで公開されています。
* 原著作権: Copyright (c) 2025 ArqAlice
* 追加・改変部分: Copyright (c) 2025-2026 Toshimiyu (Pico2 /Pico　Audiio　Pack　統合等)

------------------------------------------------------------------------------

■ 参考文献

・USB Audio Class 1.0 Specification
・Interface ラズパイPico DAC特設ページ
・REW (Room EQ Wizard)
