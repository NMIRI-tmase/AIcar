# 「AIロボットカーの自動運転ハンズオン」のPCセットアップ手順

参考：[松浦さんのページ](http://ma2.la.coocan.jp/AI_Driver/webot/view.cgi?fname=../tt02/windows.txt&fsize=5)


> [!IMPORTANT]
> 
> 以下はすべてユーザーはNmiriAdminで実行
> 

## Windowsの更新

windows Updateして22H2へ

最新の状態になるまで何度も更新・再起動

## NVIDIAドライバ

使用するWindowsで使用しているGPU → NVIDIA Quadro M1000M　（デバイスマネージャで確認）

上記に対応した最新ドライバ [URL](https://www.nvidia.co.jp/download/driverResults.aspx/212706/jp)

ドライバのバージョン：R535 U7 (537.58)

ドライバをダウンロードしてインストールすると、NVIDIAコントロールパネルがインストールされる

NVIDIAコントロールパネルでドライババージョンが537.58になっていることを確認

## VTxの有効化

windowsが起動する前に、「F2」を押してBIOSに入る

最初のメモリテストなどの画面は終了する

BIOSの設定になるので、「詳細設定」 → 「システムオプション」 → 「VTx」にチェック

変更を保存して終了

## WSLにUbuntu-22.04のインストール

> [!NOTE]
> 
> 今回はwslのversionが1だったため、以下のステップが必要だったかもしれない
>
> ```wsl —set-default-version 2```を事前に実施すればいらない可能性がある
> 

WindowsのPowerShell（NmiriAdminなので管理者権限あり）で

```
wsl —install -d Ubuntu-22.04
```

途中でユーザー名とパスワードを設定する

```
wsl -d Ubuntu-22.04
nvidia-smi
```

Not foundとなる

windowsに戻り、wslのバージョン確認
```
wsl -l -v
Ubuntu-22.04 version 1
```

nvidia-smiはバージョン１では使えないみたい（[参考](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#known-limitations)）なので、ubuntuのバージョン変更
```
wsl —-set-version Ubuntu-22.04 2
wsl -d Ubuntu-22.04
nvidia-smi
```

システム管理インターフェイスが表示される

ドライババージョン（Driver Version）が537.58になっていることを確認

> [!NOTE]
>
> BIOSでVT-xをオンにしても**仮想マシンプラットフォームが有効になっていない**と表示された場合は、「Windowsの機能」で仮想マシンプラットフォームをオンにする

## cuda toolkitのインストール

上から順に実行

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

UbuntuのcuDNN 8.7をダウンロードする　[リンク](https://developer.nvidia.com/downloads/c118-cudnn-local-repo-ubuntu2204-8708410-1amd64deb)

※ログインが必要

ダウンロードしたファイルをWSLのホームディレクトリ下に置く


上から順に実行
```
sudo apt-get install zlib1g
sudo dpkg -i cudnn-local-repo-ubuntu2204-8.7.0.84_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-*/cudnn-local-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get install libcudnn8=8.7.0.84-1+cuda11.8
sudo apt-get install libcudnn8-dev=8.7.0.84-1+cuda11.8
sudo apt-get install libcudnn8-samples=8.7.0.84-1+cuda11.8
```

## Python環境構築
```
sudo apt install python3.10-venv
python3 -m venv --system-site-packages ~/venv
source ~/venv/bin/activate
pip install --upgrade pip
pip install tensorflow
pip install pillow
pip install matplotlib
```

## 訓練環境構築
```
mkdir ~/AI_Driver
cd ~/AI_Driver
wget http://ma2.la.coocan.jp/AI_Driver/tt02/download/train.py
wget https://raw.githubusercontent.com/mituhiromatuura/AI_Driver/main/maketensor.py
sudo apt install zip unzip
sudo apt install openssh-server
```

## 環境変数

~/.bashrc の最後に以下を追加
```
source ~/venv/bin/activate
cd ~/AI_Driver
export PATH="/usr/local/cuda/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"
```

## 動作確認


```
unzip log_202309271634_d.zip
time python train.py log 6
```

GPUなし：57秒

GPUあり：45秒

## Windowsアプリのインストール

[RealVNC Viewer](https://www.realvnc.com/en/connect/download/viewer/)

[TeraTerm](https://ttssh2.osdn.jp/)

[WinSCP](https://winscp.net/eng/docs/lang:jp)

[NetworkScanner](https://www.softperfect.com/products/networkscanner/)

[VScode](https://code.visualstudio.com/)
