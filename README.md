# swift_component_background_audioplayer

```swift

/*** Set up Background Music Player, it will be used with image ***/
//arg1: Network
//arg2, arg3: Local
//arg4: Common
func setUpBackgroundAudio(urlString: String? = nil, localFileName: String? = nil, fileExtension: String? = nil, volume: Float = 10) {
    var url: URL?
    if let urlString: String = urlString {
        print("\(TAG) - setUpBackgroundAudio(): Network Audio Background SetUp")
        url = URL(string: urlString)
    } else {
        print("\(TAG) - setUpBackgroundAudio(): Local Audio Background SetUp")
        guard let localFileName: String = localFileName else {
            print("\(TAG) - No Local File Name is provided!")
            return
        }
        guard let fileExtension: String = fileExtension else {
            print("\(TAG) - setUpBackgroundAudio(): File Extension Name is not provided!")
            return
        }
        url = Bundle.main.url(forResource: localFileName, withExtension: fileExtension)
    }
    guard let unwrappedURL: URL = url else {
        print("\(TAG) - setUpBackgroundAudio(): URL is not initialized!")
        return
    }
    backgroudAudioPlayerItem = AVPlayerItem(url: unwrappedURL)
    backgroundAudioPlayer = AVPlayer(playerItem: backgroudAudioPlayerItem)
    backgroundAudioPlayer?.volume = volume
    backgroundAudioPlayer?.play()
    NotificationCenter.default.addObserver(forName: NSNotification.Name.AVPlayerItemDidPlayToEndTime, object: nil, queue: .main) { notification in
        self.backgroundAudioPlayer?.seek(to: CMTime.zero)
        self.backgroundAudioPlayer?.play()
    }
}


```
