<h1>HW 4</h1>

```swift

//
//  Referenced and modified from: https://github.com/sp71/SnowAnimationExample.
//

import Foundation
import SwiftUI
import UIKit
import WebKit

/// Manages the life cycle of the slow flake animation
internal struct SnowFlakeManager {
    
    fileprivate let snowLayer = CAEmitterLayer()
    fileprivate lazy var snowCell: CAEmitterCell = {
        let snowCell = CAEmitterCell()
        snowCell.contents = UIImage(named: "snowFlake")?.cgImage
        snowCell.scale = 0.06
        snowCell.scaleRange = 0.3
        snowCell.emissionRange = .pi
        snowCell.lifetime = 20.0
        snowCell.birthRate = 40
        snowCell.velocity = -30
        snowCell.velocityRange = -20
        snowCell.yAcceleration = 30
        snowCell.xAcceleration = 5
        snowCell.spin = -0.5
        snowCell.spinRange = 1.0
        return snowCell
    }()
    
    /// Injects snow layer into view hierarchy
    ///
    /// - Parameter view: UIView to insert snow layer within
    mutating func injectSnowLayer(into view: UIView) {
        snowLayer.emitterPosition = CGPoint(x: view.bounds.width / 2.0, y: -50)
        snowLayer.emitterSize = CGSize(width: view.bounds.width, height: 0)
        snowLayer.emitterShape = CAEmitterLayerEmitterShape.line
        snowLayer.beginTime = CACurrentMediaTime()
        snowLayer.timeOffset = CFTimeInterval(arc4random_uniform(6) + 5)
        snowLayer.emitterCells = [snowCell]
        
        view.layer.addSublayer(snowLayer)
    }
    
    /// Removes snow flake animation
    func removeFlake() {
        snowLayer.removeFromSuperlayer()
    }
    
}

// SwiftUI Wrapper for Snowflake Animation
struct SnowFlakeView: UIViewRepresentable {
    
    func makeUIView(context: Context) -> UIView {
        let view = UIView(frame: UIScreen.main.bounds)
        
        var flakeManager = SnowFlakeManager()
        flakeManager.injectSnowLayer(into: view)
        
        return view
    }
    
    func updateUIView(_ uiView: UIView, context: Context) {
        // Update the view if needed
    }
}

class SnowFlakeViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let flakeEmitterCell = CAEmitterCell()
        flakeEmitterCell.contents = UIImage(named: "snowFlake")?.cgImage
        flakeEmitterCell.scale = 0.06
        flakeEmitterCell.scaleRange = 0.3
        flakeEmitterCell.emissionRange = .pi
        flakeEmitterCell.lifetime = 20.0
        flakeEmitterCell.birthRate = 40
        flakeEmitterCell.velocity = -30
        flakeEmitterCell.velocityRange = -20
        flakeEmitterCell.yAcceleration = 30
        flakeEmitterCell.xAcceleration = 5
        flakeEmitterCell.spin = -0.5
        flakeEmitterCell.spinRange = 1.0
        
        let snowEmitterLayer = CAEmitterLayer()
        snowEmitterLayer.emitterPosition = CGPoint(x: view.bounds.width / 2.0, y: -50)
        snowEmitterLayer.emitterSize = CGSize(width: view.bounds.width, height: 0)
        snowEmitterLayer.emitterShape = CAEmitterLayerEmitterShape.line
        snowEmitterLayer.beginTime = CACurrentMediaTime()
        snowEmitterLayer.timeOffset = 10
        snowEmitterLayer.emitterCells = [flakeEmitterCell]
        
        view.layer.addSublayer(snowEmitterLayer)
    }
    
}

struct BasicImageRow: View {
    var thisCourse: Info
    var body: some View {
        HStack {
            Image(thisCourse.image)
                .resizable()
                .frame(width: 40, height: 40)
                .cornerRadius(5)
            Text(thisCourse.name)
        }
    }
}

struct ContentView: View {
    var body: some View {
        ZStack {
            VStack {
                TabView {
                    Group {
                        WelcomeView()
                            .tabItem {
                                Image(systemName: "rosette")
                                Text("Welcome")
                            }
                        InfoListView()
                            .tabItem {
                                Image(systemName: "list.dash")
                                Text("Infos")
                            }
                        WebView(urlString: "https://www.google.com/search?q=taiwan+weather&client=safari&sca_esv=596443481&bih=783&biw=1386&hl=en&sxsrf=AM9HkKmu5HjiasL6sO6QFDHIpIw7DZeKNA%3A1704681285210&source=hp&ei=RV-bZYrhCpyYvr0PkvujuAc&iflsig=AO6bgOgAAAAAZZttVWunsK45ogrdwtPxAeale-rj4dpn&ved=0ahUKEwjKl9XT4MyDAxUcjK8BHZL9CHcQ4dUDCAk&uact=5&oq=taiwan+weather&gs_lp=Egdnd3Mtd2l6Ig50YWl3YW4gd2VhdGhlcjIQEAAYgAQYigUYkQIYRhiAAjILEC4YgAQYxwEY0QMyBRAAGIAEMgUQABiABDIFEAAYgAQyBRAAGIAEMgUQABiABDIFEAAYgAQyBRAAGIAEMgUQABiABEjUC1AAWIYLcAB4AJABAJgBcaABywiqAQQxMi4xuAEDyAEA-AEBwgIKECMYgAQYigUYJ8ICBBAjGCfCAgsQABiABBiKBRiRAsICChAAGIAEGIoFGEPCAgUQLhiABMICChAuGIAEGIoFGEPCAg8QABiABBiKBRhDGEYYgALCAgoQABiABBgUGIcCwgIIEAAYgAQYywE&sclient=gws-wiz")
                            .tabItem {
                                Image(systemName: "book")
                                Text("Data")
                            }
                    }
                    .toolbarBackground(Color.black, for: .tabBar)
                    .toolbarBackground(.visible, for: .tabBar)
                }
            }
            VStack {
                Text("冬天資訊")
                    .font(.largeTitle)
                    .fontWeight(.heavy)
                    .background(.black)
                Spacer()
            }
            SnowFlakeView()
                .ignoresSafeArea(.all)
                .allowsHitTesting(false)
        }
        .environment(\.colorScheme, .dark)
    }
}

#Preview {
    ContentView()
}

struct Info : Identifiable {
    var id = UUID()
    var name: String
    var image: String
    var description: String
}

var infos = [
    Info(name: "天氣冷", image: "cold", description: "要多穿保暖外套"),
    Info(name: "天氣變化大", image: "weatherChange", description: "容易感冒，要小心"),
    Info(name: "冬天飲食", image: "hotFood", description: "多吃點熱食"),
    Info(name: "不想起床", image: "stayBed", description: "冬天真的起不來...")
]

struct InfoDetailView: View {
    @Environment(\.presentationMode) var presentationMode
    var thisInfo: Info
    
    var body: some View {
        ScrollView {
            VStack {
                Image(thisInfo.image)
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .clipped()
                Text(thisInfo.name)
                    .font(.system(.title, design: .rounded))
                    .fontWeight(.black)
                Spacer()
                Text(thisInfo.description)
                    .font(.system(.subheadline, design: .rounded))
                    .fontWeight(.light)
                Spacer()
            }
        }
        .overlay(
            HStack {
                Spacer()
                VStack {
                    Button(action: {
                        self.presentationMode.wrappedValue.dismiss()
                    }, label: {
                        Image(systemName: "chevron.down.circle.fill")
                            .font(.largeTitle)
                            .foregroundColor(.white)
                    })
                    .padding(.trailing, 20)
                    .padding(.top, 40)
                    Spacer()
                }
            }
        )
    }
}

struct InfoListView: View {
    @State var showDetailView = false
    @State var selectedinfo: Info?
    
    var body: some View {
        NavigationView {
            List(infos) { infoItem in
                BasicImageRow(thisCourse: infoItem)
                    .onTapGesture {
                        self.showDetailView = true
                        self.selectedinfo = infoItem
                    }
            }
            .sheet(item: self.$selectedinfo) { thisInfo in
                InfoDetailView(thisInfo: thisInfo)
            }
            .navigationTitle("資訊列表")
        }
    }
}

struct WebView: UIViewRepresentable {
    let urlString: String
    
    func makeUIView(context: Context) -> WKWebView {
        let webView = WKWebView()
        if let url = URL(string: urlString) {
            let request = URLRequest(url: url)
            webView.load(request)
        }
        return webView
    }
    
    func updateUIView(_ uiView: WKWebView, context: Context) {
        // You can add code here to update the web view when SwiftUI updates the view.
    }
}

struct WelcomeView: View {
    var body: some View {
        VStack {
            Image("santaHoHoHo")
                .resizable()
                .aspectRatio(contentMode: .fit)
        }
    }
}

@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

```
