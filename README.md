稍微改动了一下，本人不会编程，瞎玩，改版后无需配置更改js文件，直接导入即可食用，由于斜杠导致打不开的问题也解决了。
原作者也是国人，只是好像退坑不玩jellyfin了。

运行环境：电脑系统win11，jellyfin版本：10.8.0正式版，运行在黑群晖上。油猴版本4.16.1

教程来了：

开始前需要设置：
1、jellyfin媒体库所有文件夹添加共享的网络文件夹，像这样：![image](https://user-images.githubusercontent.com/64485323/174583830-e98c8253-200a-4638-815b-f56891bf7e36.png)

2、下载potplayer.ps1 potplayer.reg userscript.js。

3、更改potplayer.reg里的：@="powershell -File D:\\codes\\Jellyfin-Potplayer\\potplayer.ps1 %1"

更改为potplayer.ps1文件的实际路径，原文说要双反斜杠，我测试单反斜杠好像也没问题。

4、更改potplayer.ps1里的& "C:\\Program Files\\DAUM\\PotPlayer\\PotPlayerMini64.exe" $path

更改为你的PotPlayerMini64.exe实际路径，

5、双击potplayer.reg导入注册表。

6、开始使用。
说明：userscript.js文件我已经改好了，不需要任何额外的配置。不再需要将文件夹映射到本地磁盘，js脚本会直接将电影的共享文件夹路径传递给potplayer。potplayer.ps1稍微改动了一下，想办法把斜杠给去除了。如果出现powershell调用不了或者powershell一闪而过，请检查系统是否禁用脚本运行，同时检查potplayer.reg里的文件路径是否正确。win11默认禁用脚本，开启方法请百度，很简单。
测试不论是普通文件格式mkv、MP4等等，还是原盘BDMV文件夹形势或者ISO格式都能正常打开。爽歪歪啊，jellyfin各种终端对原盘支持都不怎么好，升级到10.8.0也一样，
现在存在的问题：可能是版本的问题，只能点击海报上的播放按钮才能调用PotPlayer，详情页的播放按钮没有反应，详情页点击海报也能调用，奇了怪了，我也搞不懂。

# Jellyfin-Potplayer

Tested with Jellyfin 10.5.2 and Windows 10. Newer version is not promised to work with this script. If you have any problem, please open an issue and I'll try my best to help you.

## Installation

1. Download this repository.
2. Put `potplayer.ps1` somewhere else on your disk (DO NOT delete it even after you finish this tutorial). Edit the Potplayer path in `potplayer.ps1`. Notice that you should use double backslash for divider.
3. Edit `potplayer.reg`. Change the path to where you put `potplayer.ps1`. Use double backslash also.
4. Double click `potplayer.reg` and click yes.
5. Install the extension called `Tampermonkey BETA` in your browser.
6. Edit `userscript.js`.
    1. Change `localhost:8096` to the host of your Jellyfin server.
    2. This step is tricky... We need to make sure that the path to video showed in your Jellyfin webpage is corresponding to the actual place on your computer. So, if you are using this script on the same computer which runs the Jellyfin server, just skip to step 4. If not, please right click on blank area of "This computer" window and choose "add a network location" (or something like it, I use Chinese for the system so I don't know). Link the drive where you put all the videos on your Jellyfin server to a local drive. For example, after the setup, you should be able to open (for the server) `D:/folder1/video1.mp4` by (on your local computer) `Z:/folder1/video1.mp4`.
    3.  Change line 16 in the script. Firstly, uncomment the line by deleting the leading double backslash. Then change `D:` to the drive on your server to the drive on your local computer. If you have some experience on Javascript, your can custom this path processing script by your own. Just make sure the path will be same to your local file path. For the example mentioned in step 2, the script should be like:
    ```
    path = path.replace('D:', 'Z:');
    ```
    4. Choose "Add a new script" in the `Tampermonkey` menu. Copy and paste the whole script. Make sure you enabled the script and when you open Jellyfin, the script is enabled.
7. Test by clicking on the play button! It should work fine now. Enjoy your movies!

## How it works

A user script modifies the play buttons in Jellyfin so that the web player will not be called. Instead, a link will be opened and it's a "url protocol" for Potplayer. The url protocol will firstly call a powershell script and then pass the video path to Potplayer. That's it!

