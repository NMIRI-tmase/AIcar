# 「AIロボットカーの自動運転ハンズオン」のPCセットアップのページ

参考：[松浦さんのページ](http://ma2.la.coocan.jp/AI_Driver/webot/view.cgi?fname=../tt02/windows.txt&fsize=5)


> [!IMPORTANT]
> 以下はすべてユーザーはNmiriAdminで実行

## Windowsの更新

windows Updateして22H2へ

最新の状態になるまで何度も更新・再起動

## NVIDIAドライバ

使用するWindowsで使用しているGPU → NVIDIA Quadro M1000M　（デバイスマネージャで確認）

上記に対応した最新ドライバ [URL](https://www.nvidia.co.jp/download/driverResults.aspx/212706/jp)

ドライバのバージョン：R535 U7 (537.58)

NVIDIAコントロールパネルがインストールされる

コントロールパネルでドライババージョンが537.58になっていることを確認

## VTxの有効化

windowsの起動時に「F2」を押してBIOSに入る

最初のメモリテストなどの画面は終了する

BIOSの設定になるので、「詳細設定」 → 「システムオプション」 → 「VTx」にチェック

変更を保存して終了

## WSLにUbuntu-22.04のインストール

> [!NOTE]
> 今回はwslのversionが1だったため、以下のステップが必要だったかもしれない
>
> wsl —set-default-version 2を事前に実施すればいらない可能性がある

WindowsのPowerShell(NmiriAdminなので管理者権限あり）で

```
wsl —install -d Ubuntu-22.04
```
ユーザー名とパスワードを設定

```
wsl -d Ubuntu-22.04
nvidia-smi
```

Not foundとなる

wslのバージョン確認
```
wsl -l -v
Ubuntu-22.04 version 1
```

wslのバージョン変更
```
wsl —-set-version Ubuntu-22.04 2
wsl -d Ubuntu-22.04
nvidia-smi
```

ドライババージョンが537.58になっていることを確認

## cuda toolkitのインストール

```
sudo apt-key del 7fa2af80
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-wsl-ubuntu-11-8-local_11.8.0-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-11-8-local_11.8.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-11-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
export PATH=/usr/local/cuda-11.8/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

## cuDNNのインストール

> kqh)ZiMZm8yM@ip
