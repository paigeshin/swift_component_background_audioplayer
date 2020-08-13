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
        //ambient처리해서 은은하게 재생되게 만든다.
        do {
            try  AVAudioSession.sharedInstance().setCategory(AVAudioSession.Category.ambient, options: [.allowAirPlay, .mixWithOthers])
            try  AVAudioSession.sharedInstance().setActive(true)
        } catch _ as NSError {
            print("\(TAG) - setUpBackgroundAudio(): Error happenened while setting audio session")
        }
        backgroudAudioPlayerItem = AVPlayerItem(url: unwrappedURL)
        backgroudAudioPlayerItem?.audioTimePitchAlgorithm = AVAudioTimePitchAlgorithm.lowQualityZeroLatency //로딩속도 줄이기
        
        backgroundAudioPlayer = AVPlayer(playerItem: backgroudAudioPlayerItem)
        backgroundAudioPlayer?.rate = 0.50 //로딩속도 줄이기
        backgroundAudioPlayer?.automaticallyWaitsToMinimizeStalling = false //로딩속도 줄이기
        backgroundAudioPlayer?.playImmediately(atRate: 0) //로딩속도 줄이기
        backgroundAudioPlayer?.volume = volume
        backgroundAudioPlayer?.play()
        
        /***  Debugging, 2020.08.10 ***/
        //이 코드 때문에 사운드 믹스가 안되었음. 자꾸 sound interrupting 현상이 벌어짐
        //이유는 간단함. NSNotification.Name.AVPlayerItemDidPlayToEndTime은 모든 AVPlayer의 event를 관찰하는 애들임.
        //AVQueuePlayer가 끝날 때마다, background audio player도 영향을 받게 되어있다.
        //object를 지정해줘야한다. object는 item이여야 한다.
        NotificationCenter.default.addObserver(forName: NSNotification.Name.AVPlayerItemDidPlayToEndTime, object: backgroundAudioPlayer?.currentItem, queue: .main) { [weak self] notification in
            self?.backgroundAudioPlayer?.seek(to: CMTime.zero)
            self?.backgroundAudioPlayer?.play()
        }
        
    }
```
