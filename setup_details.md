# ソフトのセットアップ

## Jetson NANO

- イメージを SD カードに焼き付けて、日本語設定その他を済ませる。

```shell
sudo apt update ;sudo apt dist-upgrade -y;sudo apt autoremove -y
sudo apt install emacs
```

### VSCode

- [参考](https://qiita.com/koppe/items/64d155bc7814441920f9)

```shell
curl -L https://github.com/toolboc/vscode/releases/download/1.32.3/code-oss_1.32.3-arm64.deb -o code-oss_1.32.3-arm64.deb
sudo dpkg -i code-oss_1.32.3-arm64.deb
```

### Chrome のログインキーリングを解除

- [参考](https://www.petitmonte.com/linux/chrome_login_keyring.html)

### pip

```shell
sudo apt install python-pip
```

## ROS

- [Ubuntu install of ROS Melodic](http://wiki.ros.org/melodic/Installation/Ubuntu)に従って、ROS をインストール。
  - `ros-melodic-desktop-full`

```shell
sudo apt install ros-melodic-rosemacs
emacs -nw ~/.emacs.d/init.el
```

- 以下を貼り付け。

```text
(setq inhibit-startup-message t)
(add-to-list 'load-path "/opt/ros/melodic/share/emacs/site-lisp")
;; or whatever your install space is + "/share/emacs/site-lisp"
(require 'rosemacs-config)
```

- ROS のワークスペース作成

```shell
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src/
catkin_init_workspace
cd ..
catkin_make
cd
emacs .bashrc &
```

- 末尾に下記を追記。

```text
source ~/catkin_ws/devel/setup.bash
```

- 必要なパッケージのインストール

```shell
sudo apt install ros-melodic-navigation
sudo apt install ros-melodic-joy
sudo apt install ros-melodic-teleop-twist-joy
sudo apt install ros-melodic-laser-filters
sudo apt install ros-melodic-slam-gmapping
```

- ユーザをグループ dialout に追加

```shell
sudo gpasswd -a $(whoami) dialout
```

- 一旦ログアウト

- pycrc のインストール

```shell
sudo pip install pycrc
```

- microbot 用ソフトをクローン

```shell
cd ~/catkin_ws/src
git clone https://github.com/KMiyawaki/oit_navigation_microbot_01.git
git clone https://github.com/KMiyawaki/oit_roboclaw_driver.git
git clone https://github.com/KMiyawaki/my_microbot_apps.git
```

- YDLidar 用ソフトをクローン

```shell
cd ~/catkin_ws/src
git clone https://github.com/YDLIDAR/ydlidar_ros.git
```

- ビルド

```shell
cd ~/catkin_ws
catkin_make
```

- YDLidar 用 udev

```shell
roscd ydlidar_ros/startup/
sudo sh initenv.sh
```

- 一旦再起動

## Wifi アクセスポイント化

- [参考](https://qiita.com/ishihatta/items/c95308265af0c2ddfea1)
- GUI で行えば良い。
- セキュリティも不要。

## Deep learning 関連

- darknet_ros(不要？)

  - [参考](https://github.com/leggedrobotics/darknet_ros)

- jetbot のソフトをインストール

```shell
cd ~
git clone https://github.com/dusty-nv/jetson-inference
cd jetson-inference
git submodule update --init
mkdir build
cd build
cmake ../
make
```

- 途中どのモデルをダウンロードするかが出てくる。とりあえず、デフォルトで。
- pyTorch のどのバージョンをインストールするか聞いてくる。とりあえず、Python2.7 と Python3.6 両方をインストール。

```shell
sudo make install

sudo apt-get install ros-melodic-vision-msgs ros-melodic-image-transport ros-melodic-image-publisher
cd ~/catkin_ws/src
git clone https://github.com/dusty-nv/ros_deep_learning
cd ~/catkin_ws
catkin_make

cd ~/catkin_ws/src
git clone https://github.com/dusty-nv/jetbot_ros

# build the package
cd ~/catkin_ws
catkin_make
```

## SainSmart IMX219 カメラ モジュール

- カメラから得られる画像にピンク色がかかっているのを修正する。
- [Jetson NANO フォーラムの記事](https://devtalk.nvidia.com/default/topic/1051913/jetson-nano/how-to-close-gstreamer-pipeline-in-python/post/5340291/#5340291)
- ファイルを[ダウンロード](https://www.dropbox.com/s/ougz816496m4ilw/camera_overrides.isp?dl=0)
- `/var/nvidia/nvcam/settings/camera_overrides.isp`として配置し、下記コマンドを実行。

```shell
sudo chmod 664 /var/nvidia/nvcam/settings/camera_overrides.isp
sudo chown root:root /var/nvidia/nvcam/settings/camera_overrides.isp
```
