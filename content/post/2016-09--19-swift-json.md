---
layout: post
title: Swift 使用JSON数据结构
date: 2016-09-19 23:25:00
categories: ["iOS"]
tags: ["iOS"]
mathjax: true
---

如果你的APP从服务器获取到的数据格式为JSON。你可以使用[JSONSerialization](https://developer.apple.com/reference/foundation/nsjsonserialization)把JSON解析成Swift的数据类型，比如`Dictionary`,`Array`,`String`,`Number`,`Bool`。不过，因为你的APP不能直接使用JSON的结构，可以将它解析成模型对象。本文描述了一些方法可以让你的APP使用JSON数据。

**从JSON中取值**

`JSONSerialization`中有个方法`jsonObject(with:options:)`可以返回一个类型为`Any`的数据，并且如果`data`数据不能被解析会返回错误信息
```swift
import Foundation

let data: Data // 从网络请求下来的数据
let json = try? JSONSerialization.jsonObject(with: data, options: [])

```

尽管JSON数据中只包含一个值，每次请求都会返回一个对象或者数组。可以使用`optional`类型使用`as？`，在`if`或者`guard`条件语句中提取出自定义的数据。从JSON中提取`Dictionary`数据需要元素条件是`[String: Any]`。提取`Array`类型数据需要的元素条件是`[Any]`（或者更加明确的数组元素类型，比如`[Strng]`）。可以使用字典的键值或者数组的下标配合元素的类型来获取对应的数据。
```swift
// JSON中对象
/*
	{
		"someKey": 42.0,
		"anotherKey": {
			"someNestedKey": true
		}
	}
*/
if let dictionary = jsonWithObjectRoot as? [String: Any] {
	if let number = dictionary["someKey"] as? Double {
		// access individual value in dictionary
	}

	for (key, value) in dictionary {
		// access all key / value pairs in dictionary
	}

	if let nestedDictionary = dictionary["anotherKey"] as? [String: Any] {
		// access nested dictionary values by key
	}
}

// JSON中是数组
/*
	[
		"hello", 3, true
	]
*/
if let array = jsonWithArrayRoot as? [Any] {
	if let firstObject = array.first {
		// access individual object in array
	}

	for object in array {
		// access all objects in array
	}

	for case let string as String in array {
		// access only string values in array
	}
}
```

Swift包含基础API可以安全、快速的提取和处理JSON数据。

**根据JSON创建模型对象**
大多数APP遵循`MVC`设计模式，通常将JSON转成APP中指定的`模型对象`。

例如，编写一个为搜索当地参观的APP，你可能需要创建一个接收JSON数据并初始化为餐厅模型的方法，用一个`异步`网络请求，然后返回一个包含餐厅对象的数组。

例如下面的餐厅模型：
```swift
import Foundation

struct Restaurant {
	enum Meal: String {
		case breakfast, lunch, dinner
	}

	let name: String
	let location: (latitude: Double, longitude: Double)
	let meals: Set<Meal>
}
```

餐厅有一个`String`类型的名字，一个`位置坐标`，和一个包含`进餐类型`的`枚举值`。

下面可能是一个服务器返回的餐厅数据：
```swift
{
	"name": "Caffè Macs",
	"coordinates": {
		"lat": 37.330576,
		"lng": -122.029739
	},
	"meals": ["breakfast", "lunch", "dinner"]
}
```

### 写一个JSON的可选型初始值

从JSON中初始化一个餐厅对象，将JSON数据提取和转换成一个`Any`类型的对象
```swift
extension Restaurant {
	init?(json: [String: Any]) {
		guard let name = json["name"] as? String,
			let coordinatesJSON = json["coordinates"] as? [String: Double],
			let latitude = coordinatesJSON["lat"],
			let longitude = coordinatesJSON["lng"],
			let mealsJSON = json["meals"] as? [String]
		else {
			return nil
		}

		var meals: Set<Meal> = []
		for string in mealsJSON {
			guard let meal = Meal(rawValue: string) else {
				return nil
			}

			meals.insert(meal)
		}

		self.name = name
		self.coordinates = (latitude, longitude)
		self.meals = meals
	}
}
```

如果你的APP服务器不会只返回给一种模型对象，应该考虑实现不同的初始化方法处理每一种可能的类型。
在上面的示例中，提取到的每个常量的值是通过JSON使用`可选值`指定为字典。
`进餐名称`提取的数据只能作为初始值。`经度`和`纬度`可以组合成一个`元组`。进餐的类型可以使用枚举值来表示。

**处理JSON初始化错误**
上面的示例实现了一个可选型的初始化，如果失败，则返回`nil`。或者你可以定义一个符合协议的类型来表示初始化错误，当反序列化失败的时候抛出一个错误类型。
```swift
enum SerializationError: Error {
	case missing(String)
	case invalid(String, Any)
}

extension Restaurant {
	init(json: [String: Any]) throws {
		// Extract name
		guard let name = json["name"] as? String else {
			throw SerializationError.missing("name")
		}

		// Extract and validate coordinates
		guard let coordinatesJSON = json["coordinates"] as? [String: Double],
			let latitude = coordinatesJSON["lat"],
			let longitude = coordinatesJSON["lng"]
		else {
			throw SerializationError.missing("coordinates")
		}

		let coordinates = (latitude, longitude)
		guard case (-90...90, -180...180) = coordinates else {
			throw SerializationError.invalid("coordinates", coordinates)
		}

		// Extract and validate meals
		guard let mealsJSON = json["meals"] as? [String] else {
			throw SerializationError.missing("meals")
		}

		var meals: Set<Meal> = []
		for string in mealsJSON {
			guard let meal = Meal(rawValue: string) else {
				throw SerializationError.invalid("meals", string)
			}

			meals.insert(meal)
		}

		// Initialize properties
		self.name = name
		self.coordinates = coordinates
		self.meals = meals
	}
}
```

在这里，餐厅类型嵌套的声明一个`SerializationError`的枚举类型，定义枚举值的属性为`相关值缺失`或者`相关值无效`。在这一版本的JSON初始化中，不再是通过返回`nil`表示失败，而是返回一个具体的`错误原因`。这一版本中还加入了对数据的`验证`(确保坐标值代表一个有效的`地理坐标`,每个`进餐名字`对应指定的枚举值)。

**获取结果的方法**

服务器通常在一个JSON中返回多个结果。例如，`搜索`服务可能返回包含`0`个或者`多个`餐厅及匹配的`请求参数`，还包括其他的一些数据：
```swift
{
	"query": "sandwich",
	"results_count": 12,
	"page": 1,
	"results": [
		{
			"name": "Caffè Macs",
			"coordinates": {
				"lat": 37.330576,
				"lng": -122.029739
			},
			"meals": ["breakfast", "lunch", "dinner"]
		},
		...
	]
}
```

你可以创建一个类方法，将一个相应的餐厅对象转换成查询方法的`参数`，通过http请求发送给服务器。这段代码同时负责`响应`和`处理`服务器返回的JSON数据，`异步处理`JSON中返回的`数组结果`,反序列化成`餐厅对象`。
```swift
extension Restaurant {
	private let urlComponents: URLComponents // base URL components of the web service
	private let session: URLSession // shared session for interacting with the web service

	static func restaurants(matching query: String, completion: ([Restaurant]) -> Void) {
		var searchURLComponents = urlComponents
		searchURLComponents.path = "/search"
		searchURLComponents.queryItems = [URLQueryItem(name: "q", value: query)]
		let searchURL = searchURLComponents.url!

		session.dataTask(url: searchURL, completion: { (_, _, data, _)
			var restaurants: [Restaurant] = []

			if let data = data,
				let json = try? JSONSerialization.jsonObject(with: data, options: []) as? [String: Any] {
				for case let result in json["results"] {
					if let restaurant = Restaurant(json: result) {
						restaurants.append(restaurant)
					}
				}
			}

			completion(restaurants)
		}).resume()
	}
}
```
当用户在搜索栏输入文本的时候，视图控制器调用这个方法对餐厅进行匹配：
```swift
import UIKit

extension ViewController: UISearchResultsUpdating {
	func updateSearchResultsForSearchController(_ searchController: UISearchController) {
		if let query = searchController.searchBar.text, !query.isEmpty {
			Restaurant.restaurants(matching: query) { restaurants in
				self.restaurants = restaurants
				self.tableView.reloadData()
			}
		}
	}
}
```
即便是网络服务的细节发生变化，视图控制器只是以用`分离封装`的接口方法访问餐厅数据，与网络服务部分`降低耦合`。

参考苹果的[关于Swift使用JSON的官方博客](https://developer.apple.com/swift/blog/)