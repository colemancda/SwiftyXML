#SwiftyXML

 ![Platform](https://img.shields.io/badge/platforms-iOS%208.0+%20%7C%20macOS%2010.10+%20%7C%20tvOS%209.0+%20%7C%20watchOS%202.0+-333333.svg)

SwiftyXML use most swifty way to deal with XML data.

Sample XML: 

```xml
<catalog>
	<product description="Cardigan Sweater" product_image="cardigan.jpg" >
		<catalog_item gender="Men's" >
			<item_number>QWZ5671</item_number>
			<price>39.95</price>
			<size description="Medium" >
				<color_swatch image="red_cardigan.jpg" >Red</color_swatch>
				<color_swatch image="burgundy_cardigan.jpg" >Burgundy</color_swatch>
			</size>
			<size description="Large" >
				<color_swatch image="red_cardigan.jpg" >Red</color_swatch>
				<color_swatch image="burgundy_cardigan.jpg" >Burgundy</color_swatch>
			</size>
		</catalog_item>
		<catalog_item gender="Women's" >
			<item_number>RRX9856</item_number>
			<price>42.50</price>
			<size description="Small" >
				<color_swatch image="red_cardigan.jpg" >Red</color_swatch>
				<color_swatch image="navy_cardigan.jpg" >Navy</color_swatch>
				<color_swatch image="burgundy_cardigan.jpg" >Burgundy</color_swatch>
			</size>
		</catalog_item>
	</product>
</catalog>
```

With SwiftyXML all you have to do is:

```swift
let xml = XML(data: xmlFileData)
let color = xml["product"]["catalog_item"]["size"]["color_swatch"][1].string //return "Burgundy"
let price = xml["product"]["catalog_item"]["price"].float // 39.95
```

This is same as below, SwiftyXML will auto pick the first element as default: 

```swift
let xml = XML(data: xmlFileData)
let color = xml["product"][0]["catalog_item"][0]["size"][0]["color_swatch"][1].string //return "Burgundy"
```

What about if you input some wrong keys:

```swift
let xml = XML(data: xmlFileData)
let color = xml["product"]["catalog_item"]["wrong_size"]["wrong_color"][1].string //return ""
//output: [SwiftyXML]:["product"][0]["catalog_item"][0]: no such children named: "wrong_size"
//you can easily found the issue and fix it
```

## Requirements

- iOS 8.0+ | macOS 10.10+ | tvOS 9.0+ | watchOS 2.0+
- Xcode 8

##Integration

####Swift Package Manager
You can use [The Swift Package Manager](https://swift.org/package-manager) to install `SwiftyXML` by adding the proper description to your `Package.swift` file:
```swift
import PackageDescription

let package = Package(
    name: "PROJECT_NAME",
    targets: [],
    dependencies: [
        .Package(url: "https://github.com/chenyunguiMilook/SwiftyXML.git", majorVersion: 1)
    ]
)
```

## Usage

####Initialization
```swift
import SwiftyXML
```
```swift
let xml = XML(data: xmlFileData)
```
####Access XML
```swift
// handle xml
if let xml = xml["product"]["catalog_item"]["size"]["color_swatch"].xml {
    if  let color = xml.value?.string,
        let image = xml.attributes["image"]?.string {
        print(color) // "Red\n"
        print(image) // "red_cardigan.jpg\n"
    }
}
```

####Print out the error

```swift
if let color1 = xml["product"]["catalog_item"]["wrong_size"]["wrong_color"][1].xml {
    // do stuff ~
} else {
    print(xml["product"]["catalog_item"]["wrong_size"]["wrong_color"][1].error)
    //["product"][0]["catalog_item"][0]: no such children named: "wrong_size"
}
```

#### Catch the error 

```swift
do {
    let color1 = try xml["product"]["catalog_item"]["wrong_size"]["wrong_color"][1].getXML()
} catch {
    print(error) //failue(["product"][0]["catalog_item"][0]: no such children named: "wrong_size")
}
```

#### Access XML Attributes

```swift
// handle xml attributes
let attributes = xml["product"]["catalog_item"][1]["size"]["color_swatch"].attributes
if let image = attributes["image"]?.string {
    print(image) // "red_cardigan.jpg\n"
}

// or non optional result
let image = xml["product"]["catalog_item"][1]["size"]["color_swatch"].attribute(of: "image")
```
#### Access XML List

```swift
// handle xml list
for catalog in xml["product"]["catalog_item"] {
    for size in catalog["size"] {
        if let description = size.attributes["description"] {
            print(description) //3 times: Medium Large Small
        }
    }
}
```
#### Read Enums

```Swift
// read enum value
public enum Color : String {
    case Red, Navy, Burgundy
}
let c: Color = xml["product"]["catalog_item"]["size"]["color_swatch"].value() //Red
```

####Debugging

```swift
// enable debugger, default is true
XML.debugEnabled = true

// implement you own Logger, you can save the log to file, or showing on debug pannel
public class MyLogger : XMLLogger {
    public var messages: [String] = []
    
    public func log(_ message: String) {
        messages.append(message)
    }
    public func saveLog(to url:URL) {
        // save out you logger
    }
}

let logger = MyLogger()
XML.debugLogger = logger
```

#### Construct XML

```swift
let store = XML(name: "store")
store.addAttribute(name: "description", value: "Ball Store")

let product1 = XML(name: "product")
product1.addAttribute(name: "name", value: "football")
product1.addAttribute(name: "weight", value: 0.453)

let product2 = XML(name: "product")
product2.addAttribute(name: "name", value: "basketball")
product2.addAttribute(name: "weight", value: 0.654)

store.addChild(product1)
store.addChild(product2)

print(store.toXMLString())
```

```xml
// store xml output
<store description="Ball Store" >
	<product name="football" weight="0.453" />
	<product name="basketball" weight="0.654" />
</store>
```