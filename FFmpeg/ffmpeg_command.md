![image](https://github.com/Tinywan/PHP_Experience/blob/master/Public/Common/static/images/ffmpeg.png)
## FFmpeg 常用命令DEMO
### :paw_prints:常用
*  为了设定输出视频码率为64kbit/s：(码率转换很慢，尤其高码率到低码率，如：2312kbps=>1024kbps)     
    `ffmpeg -i input.avi -b:v 64k -bufsize 64k output.avi`
*  为了切换帧率到24fps：     
    `ffmpeg -i input.avi -r 24 output.avi`
*  将输入的1920x1080缩小到960x540输出   
    `ffmpeg -i ./test.mkv -vf scale=960:540 960_540.mp4`
    
### :bouquet:视频
*  FFmpeg concat 协议：     
    `ffmpeg -i "concat:02.mp4|03.mp4" -c copy concat_output.mp4`
*  视频合并：   
    - 方法一：FFmpeg concat 协议（测试通过）    
    `ffmpeg -i "concat:input1.mpg|input2.mpg|input3.mpg" -c copy output.mpg`   
    - 方法一：FFmpeg concat 协议    
        `ffmpeg -i "concat:02.mp4|03.mp4" -c copy concat_output.mp4`  
    
### :cherry_blossom: 音频
*  FFmpeg concat 协议 :satellite: ：     
    `ffmpeg -i "concat:02.mp4|03.mp4" -c copy concat_output.mp4`    

### :tulip:复合流
*  第一路流的视频：  
`ffmpeg -i ./0001.mp4 -i ./0002.mp4 -map 0:0  -c copy -y ./0001_video.mp4`    

*  第一路流的音频：  
`ffmpeg -i ./0001.mp4 -i ./0002.mp4 -map 0:1  -c copy -y ./0001_video.mp4`    

*  第二路流的视频：  
`ffmpeg -i ./0001.mp4 -i ./0002.mp4 -map 1:0  -c copy -y ./0001_video.mp4`    

*  第二路流的音频：  
`ffmpeg -i ./0001.mp4 -i ./0002.mp4 -map 1:1  -c copy -y ./0001_video.mp4`    

*  第一路、第二路流的混合：  
`ffmpeg -i ./0001.mp4 -i ./0002.mp4 -map 0:0 -map 0:1 -map 1:0 -map 1:1  -c copy -y ./0001_0002_audio_video.mp4`    


### :four_leaf_clover:摄像头
*  直接抓取笔记本摄像头视频和音频到本地存储     
    `ffmpeg -f dshow -i video="USB Camera":audio="麦克风 (Conexant SmartAudio HD)" -vcodec libx264 -acodec copy -preset:v ultrafast ./tinywan_computer_out.mpg`    

### :rose:图片流
*  strfime选项允许你导出按时间/日期信息命名的文件 "%Y-%m-%d_%H-%M-%S" 模板       
    `ffmpeg -i rtmp://live.hkstv.hk.lxdns.com/live/hks -f image2 -strftime 1 "%Y-%m-%d_%H-%M-%S.jpg"`    
*  每隔20秒截一张图       
    `ffmpeg -i input.flv -f image2 -vf fps=fps=1/20 out%d.png`    
*  strfime选项允许你导出按时间/日期信息命名的文件 "%Y-%m-%d_%H-%M-%S" 模板       
    `ffmpeg -i rtmp://live.hkstv.hk.lxdns.com/live/hks -f image2 -strftime 1 "%Y-%m-%d_%H-%M-%S.jpg"`    
*  Gif图片转换MP4格式视频   
    `ffmpeg -i ./tinywanGif.gif -movflags faststart -pix_fmt yuv420p -vf "scale=trunc(iw/2)*2:trunc(ih/2)*2" -f mp4 ./TinywanGifvideo.mp4`
*  视频添加图片水印  
    - [x] 单个图片，测试成功   
    `ffmpeg -i out.mpg -vf "movie=amailogo.png [watermark]; [in][watermark] overlay=10:10" tinywanVideologo.mp4`
   
### :sunflower:RTSP
*  发送流到RTSP服务器     
    `ffmpeg -rtsp_transport tcp -i "rtsp://192.168.18.240:554/onvif/live/1" -vcodec copy -f rtsp -muxdelay 0.1 rtsp://server/live.sdp`    
    `ffmpeg -re -i /input.avi -f rtsp -muxdelay 0.1 rtsp://12.34.56.78:5545/abc` 
     - __注意__ ：`如果是本地视频，-re一定要加，代表按照帧率发送，否则ffmpeg会一股脑地按最高的效率发送数据`
     - __建议__ ：`如果拉取的是一个摄像头的话，-re不要加`
     - 参数 `-re`：按时间戳读取文件(另外一种是直接读取,文件被迅速读完)
     - 参数 `-vcodec copy`：要加，否则ffmpeg会重新编码输入流格式
     - 参数 `-rtsp_transport`：传输协议，默认UDP   
     
### :hibiscus:网络流和本地流切换
*  使用FFmpeg转录网络直播流     
    - `ffmpeg -i http://60.199.188.151/HLS/WG_ETTV-N/index.m3u8 d:\cap.mp4`：CPU消耗很大    
    - `ffmpeg -i http://60.199.188.151/HLS/WG_ETTV-N/index.m3u8 -c:v copy -c:a copy -bsf:a aac_adtstoasc d:\cap.mp4`：CPU消耗很小    
*  把文件当做直播推送至服务器 (RTMP + FLV)     
    `ffmpeg - re -i demo.mp4 -c copy - f flv rtmp://w.gslb.letv/live/streamid`  
*  将直播的媒体保存到本地     
    `ffmpeg -i rtmp://r.glsb.letv/live/streamid -c copy streamfile.flv`
*  将一个直播流，视频改用h264压缩，音频改用faac压缩，送至另一个直播服务器    
    `ffmpeg -i rtmp://r.glsb.letv/live/streamidA -c:a libfaac -ar 44100 -ab 48k -c:v libx264 -vpre slow -vpre baseline -f flv rtmp://w.glsb.letv/live/streamb`
*  将一个高清流，复制为几个不同视频清晰度的流重新发布，其中音频不变     
    `ffmpeg -re -i rtmp://server/live/high_FMLE_stream -acodec copy -vcodec x264lib -s 640×360 -b 500k -vpre medium -vpre baseline rtmp://server/live/baseline_500k -acodec copy -vcodec x264lib -s 480×272 -b 300k -vpre medium -vpre baseline rtmp://server/live/baseline_300k -acodec copy -vcodec x264lib -s 320×200 -b 150k -vpre medium -vpre baseline rtmp://server/live/baseline_150k -acodec libfaac -vn -ab 48k rtmp://server/live/audio_only_AAC_48k`
*  将直播的媒体保存到本地     
    `ffmpeg -i rtmp://r.glsb.letv/live/streamid -c copy streamfile.flv`
  
      

    
