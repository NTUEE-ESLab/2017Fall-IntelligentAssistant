# 2017Fall-IntelligentAssistant
## 網址
[https://ntuee-eslab.github.io/2017Fall-IntelligentAssistant/](https://ntuee-eslab.github.io/2017Fall-IntelligentAssistant/)
## Hardware requirement
- for Rpi
	1. USB音效卡、麥克風、喇叭
	2. LED
- for Arduino
 	1. RedBear BLE shield
 	2. L298N
 	3. 直流馬達(小電扇)

## Usage
    (請見網頁的Usage部分)
## Progress

### Tizonia
- http://tizonia.org/

是一個整合市面上音樂串流平台的專案，目標是能夠透過command line放歌。
我們之所以選擇YouTube是因為此專案對YouTube的支援最高，可以達到我們的需求

### 網路爬蟲
### 1. 天氣
原本想要使用中央氣象局的API，然而後來發現他的API需要將資料下載下來，且格式是XML等，因我們比較熟悉csv或是json，所以反而比較不方便，最後決定直接去中央氣象局官網把資料爬下來
![](https://galagege.weebly.com/uploads/1/1/6/8/116847711/126461243.png)

上圖網頁中的地點可以點擊，進去後會有更詳細的預報
因此我們將詳細預報對應的網址做成一個dictionary，透過語音下達指令後，程式會去抓取對應地點的詳細預報
### 2. 新聞
我們實作了抓取蘋果日報網頁的熱門新聞爬蟲。除了爬取排行和新聞標題之外，程式還會進一步進到每則新聞的頁面，並把文章也都存起來。而因為此網頁會隨時根據熱門度更新，因此除了在使用者第一次使用新聞功能時會爬取之外，當使用者再次使用此功能時，如果程式已爬的資料已經超過一定時間(我們設為1小時)，則程式會再去爬一次。



### DHT11
我們使用DHT11來量測溫度及濕度。主要用途為使用可以可向智慧管家詢問，以及量測溫度變化來改變電風扇的強度。
在主程式中我們使用subprocess這個套件來執行下面兩個bash指令
```python=
# insert dht11 driver
sudo insmod dht11.ko
# get temperature and humiy
sudocat /dev/DHT11
```

### Subprocess
除了上面的DHT11有使用到subprocess之外，我們也在許多地方使用了這個套件，因為他不但可以透過command line執行程式，還能夠kill掉呼叫的process。
例如在播報新聞以及播放音樂的時候，使用者可能會想要中斷正在播放的內容。因此我們有增加這樣的功能：在播放上述內容的時候，當程式偵測到中斷的條件時會send SIGINT 的 signal到執行中的subprocess來中斷它

### Pexpect
Pexpect套件是用來執行gatttool的工具，比起Subprocess，他對其他process的互動比較強大，因此可以滿足我們拿來執行gatttool並與Arudino互動的需求。

### Speech Recognition
使用SpeechRecognition套件，來做語音辨識的工作，套件內含八種不同的語音辨識介面。我們取用的是google speech recognition，並使用公用的google金鑰，傳入麥克風收到的音訊，將語音訊息傳到google端運算，並回傳辨識出來的text。
辨識速度受到網路速度影響大，平均辨識速度約在1到2秒左右，長的字串需更多時間，有時會有卡住的問題，所以另外裝了一個LED燈來當作是否以準備好接收語音的信號。

### gTTS
gTTS(google text to speech)是一個簡單的小工具，主要做的事情是將輸入的text，丟到google translate上，抓取其音訊，再存成mp3檔。可將其import至python code裡去call他的function，亦可以利用command line來生成想要的音訊檔案。
在這裡我們用來播放聲音訊息的軟體為mpv player。
