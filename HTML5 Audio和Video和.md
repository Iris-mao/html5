#### Audio和Video API
* 两个重要概念：容器+解码器
* 视频容器中包括：音频轨道+视频轨道+一些其他元数据（封面、标题、子标题、字幕等等），视频格式包括avi、flv、mp4、mkv、ogv等
* 解码器：音频解码器包括AAC\MPEG-3\Ogg Vorbis，视频解码器包括H.264\vp8\Ogg Theora
* 使用API的好处：1. 作为浏览器的原生支持功能，无需安装 2. 媒体元素向web页面提供了通用、集成和可脚本制作的API
* vedio浏览器支持性检测(控制台返回为true则支持)
```html
<!DOCTYPE html>
<html>
<title>HTML5 Video</title>
</html>
<body></body>
<script>
    var hasVideo = !!(document.createElement('video').canPlayType);
    console.log(hasVideo);
</script>
```
* 简单的vedio元素（为了防止浏览器不支持HTML5视频，可以将以flash方式插入的视频放在相同的位置作为备选内容）
```html
<!DOCTYPE html>
<html>
<title>HTML5 Video</title>
</html>
<body>
<video src="video.webm" controls>
    Your broswer does not support HTML5 video.
</video>
</body>
```
* 在影片中添加字幕，首先创建一个`subtitles_en.vtt`文件，然后将该文件嵌入到vedio中
```
WEBVTT
1
00:00:01.000 --> 00:00:03.000
What do you think about HTML5 Video and WebVTT?...
2
00:00:04.000 --> 00:00:08.000
I think it's great.I can't wait for all the browsers to support it!
```
```html
<video src="video.webm" controls>
    <track label="English" kind="subtitles" srclang="en" src="subtitles_en.vtt" default>
    Your browser does not support HTML5 video.
</video>
```
* audio音频文件的加载和vedio类似
```html
<!DOCTYPE html>
<html>
<title>HTML5 Audio</title>
</html>
<audio src="Backroad.ogg" controls>
    An audio clip from Johann Sebastian Bach.
</audio>
```
* 为了防止浏览器不支持相关容器或者编解码器，可以用source进行备用声明，浏览器会根据自身播放能力自动选择，挑选最佳的来源进行播放（可以在source元素中制定type属性，指明容器类型和解码方式）
```html
<audio controls>
    <source src="Backroad.ogg">
    <source src="Backroad.mp3">
    An audio clip from Johann Sebastian Bach.
</audio>
```
* vedio和audio中的`controls`属性是为了抗议用户进行控制，如果不写这个属性，音频文件就看不见界面了，为了防止这种情况的发生，不写`controls`属性的时候要写`autoplay`属性，文件就会自动播放了
* 如果内置的控件不适应用户界面的布局，或者希望可以通过默认控件中没有的的条件和动作来来控制音频或视频，可以通过一些内置的javascript函数和特性。具体的不做列举了
* 添加play按钮控制音频
```
<!DOCTYPE html>
<html>
<title>HTML5 Audio</title>
</html>
<audio id="clickSound">
    <source src="Backroad.ogg">
</audio>
<button id="toggle" onclick="toggleSound()">Play</button>
<script type="text/javascript">
    function toggleSound() {
        var music = document.getElementById("clickSound");
        var toggle = document.getElementById("toggle");

        if(music.paused){
            music.play();
            toggle.innerHTML = "Pause";
        }else {
            music.pause();
            toggle.innerHTML = "Play";
        }
    }
</script>
```
* 完整实例（每隔5s中抓取一个视频帧，将该帧展示在canvas中，点击canvas对应的帧，视频会从对应帧的位置重新开始播放）
```
<!DOCTYPE html>
<html>
<title>HTML5 Video</title>
<video id="movies" autoplay oncanplay="startVideo()" onended="stopTimeLine()"
       autobuffer="true" width="400px" height="300px">
    <source src="video.webm">
</video>
<canvas id="timeline" width="400px" height="300px"></canvas>
<script>
    //抓取帧的时间间隔：单位是ms
    var updateInterval = 5000;
    //时序中帧的尺寸
    var frameWidth = 100;
    var frameHeight = 75;

    //时序的总帧数
    var frameRows = 4;
    var frameColumns = 4;
    var frameGrid = frameRows * frameColumns;

    //当前帧
    var frameCount = 0;

    //播放完后取消计时器
    var intervalId;

    var videoStarted = false;

    //把帧绘制到画布上
    function updateFrame() {
        var video = document.getElementById("movies");

        var ctx = timeline.getContext("2d");

        //根据帧计算出当前播放位置，然后以视频为输入参数，绘制图像
        var framePosition = frameCount % frameGrid;
        var frameX = (framePosition % frameColumns) * frameWidth;
        var frameY = (Math.floor(framePosition / frameRows)) * frameHeight;
        ctx.drawImage(video,0,0,400,300,frameX,frameY,frameWidth,frameHeight);

        frameCount++;
    }

    function startVideo() {
        //只在视频第一次播放的时候设置计时器
        if(videoStarted)
            return;

        //计算初始帧，然后以规定时间间隔创建其他帧
        updateFrame();
        intervalId = setInterval(updateFrame,updateInterval);

        //单击帧时设置处理器搜索视频
        var timeline = document.getElementById("timeline");
        timeline.onclick = function (evt) {
            var offX = evt.layerX - timeline.offsetLeft;
            var offY = evt.layerY - timeline.offsetTop;

            //从索引0开始计算单击的是网络中的哪个帧
            var clickedFrame = Math.floor(offY/frameHeight)*frameRows;
            clickedFrame += Math.floor(offX/frameWidth);

            //打开启频后，找到实际帧
            var seekFrame = (((Math.floor(frameCount/frameGrid))*frameGrid)+clickedFrame);
            //如果用户单击当前帧前面的帧，那么假设它是最后一帧
            if(clickedFrame>(frameCount%16))
                seekFrame-=frameGrid;

            //不能再视频播放前搜索
            if(seekFrame<0)
                return;

            //搜索视频到那一帧（以秒为单位）
            var video = document.getElementById("movies");
            video.currentTime = seekFrame*updateInterval/1000;

            //然后将帧数设置给目标
            frameCount = seekFrame;
        }
    }

    //停止收集时间线帧
    function stopTimeLine() {
        clearInterval(intervalId);
    }
</script>
</html>
```
重点：计算canvas中每一个帧存放的位置，drawImage（）函数，计算点击的canvas中帧是第几个（目前没有完全弄懂）