<h1>日期顯示作業</h1>

```swift

import SwiftUI

struct ContentView: View {
    
    @State var displayFontSelected = 0
    @State var isDeepScheme = false
    @State var colorArray: Array<Double> = [255.0, 255.0, 255.0]
    @State var stepperValue = 0
    @State var sliderValue = 0.0
    @State var dateChosen = Date()
    
    let displayFontType = [".default", ".rounded", ".monospaced", ".serif"]
    let formatter = DateFormatter()
    
    init() {
        formatter.dateFormat = "yyyy-MM-dd"
    }
    
    var body: some View {
        NavigationView {
            Form(content: {
                Section(header: Text("字型設定"), content: {
                    Picker(selection: $displayFontSelected, label: Text("字型選擇(\(displayFontSelected))")) {
                        ForEach(0..<displayFontType.count, id: \.self, content: {
                            Text(self.displayFontType[$0])
                        })
                    }
                })
                Section(header: Text("背景風格"), content: {
                    Toggle(isOn: $isDeepScheme) {
                        Text("深色(\(String(isDeepScheme).capitalized))")
                    }
                })
                Section(header: Text("計數器"), content: {
                    Stepper(
                        onIncrement: {stepperValue+=1},
                        onDecrement: {stepperValue-=1},
                        label: {
                            Text("Stepper (\(stepperValue))")
                        }
                    )
                })
                Section(header: Text("滑桿(\(sliderValue, specifier: "%.2f"))"), content: {
                    Slider(value: $sliderValue, in: 0...1)
                })
                Section(header: Text("日期"), content: {
                    DatePicker(selection: $dateChosen, displayedComponents: .date) {
                        Text(formatter.string(from: dateChosen))
                    }
                    .environment(\.locale, Locale(identifier: "zh_Hant_TW"))
                })
            })
            .navigationBarTitle("Settings 設定")
        }
    }
}


```

