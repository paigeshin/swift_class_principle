# How to organize class

- Class Name
    - Private Properties
    - Public Properties
    - Initializers/Constructors
    - Functions

# Class Should be small

- Classes Should be Small (Single Responsibility Principle)

# Cohesion

- Cohesion refers to what the class (or module) can doLow Cohesion
- Single Responsibility Principle is the result of Cohesion

### Low Cohesion

- Class does a great variety of actions - it is broad, unfocused on what it should do

### High Cohesion

- Class is focused on what it should be doing

### Low Cohesion Class

- Staff
    - checkEmail()
    - sendEmail()
    - emailValidate()
    - printLetter()

⇒ Not focused on staff 

### High Cohesion Class

- Staff
    - salary
    - emailAddress()
    - setSalary(salary)
    - getSalary()
    - setEmailAddress(email)
    - getEmailA

⇒ Focused on Staff

⇒ Only related to Staff

# Open-Closed Principle Example 1

- Prepare for Software Changes

 

### Problem

- The problem with this code,
- When you need to add some features, you need to new properties or new methods

```swift
import Foundation

// The problem with this code, 
// When you need to add some features, you need to new properties or new methods
class Sql {

    var table: String 
    var columns: [String]

    init(table: String, columns: [String]) {
        self.table = table 
        self.columns = columns 
    }

    func create() -> String {
        return "" 
    }

    func insert(fields: [AnyObject]) {

    }

    func selectAll() {

    }

}
```

### Refactored following OCP Principle

```swift
import Foundation

protocol Sql {
    func generate() -> String 
}

class InsertSQL: Sql {

    var table: String 
    var columns: [String]
    var values: [String]

    init(table: String, columns: [String], values: [String]) {
        self.table = table 
        self.columns = columns
        self.values = values 
    }

    func generate() -> String {
        return "INSERT INTO \(self.table)(\(columns.joined(separator: ","))) VALUE(\(self.values.joined(separator: ",")))" 
    }

}

// New Feature
class SelectWithCriteria: Sql {

    var table: String 
    var columns: [String]
    var criteria: String  

    init(table: String, columns: [String], criteria: String) {
        self.table = table 
        self.columns = columns
        self.criteria = criteria
    } 

    func generate() -> String {
        return ""
    }

}

func executeSql(sql: Sql) {
    let sqlAsString = sql.generate()
    // execute the sql 
} 

let insertSql = InsertSQL(table: "Customers", columns: ["firstName", "lastName"], values: ["Paige", "Shin"])
let result = insertSql.generate()

print(result)
```

# Open-Closed Principle Example 2

- Isolation

```swift
import UIKit
import XCTest

protocol StockExchange {
    func currentPrice(symbol: String) -> Double
}

class NYStockExchange: StockExchange {
    func currentPrice(symbol: String) -> Double {
        return 10.0
    }
}

// Property Injection through protocol
// Constructor Injection through protocol
// Method Injection through protocol
class Porfolio {
    
    var exchange: StockExchange
    var value: Double = 0.0
    
    init(exchange: StockExchange) {
        self.exchange = exchange
    }
    
    func add(quantity: Int, symbol: String) {
        let price = self.exchange.currentPrice(symbol: symbol)
        value = Double(quantity) * price
    }
    
}

class FixedStockExchangeStub: StockExchange {
    
    private var symbolAndPrice: [String: Double] = [:]
    
    func fix(symbol: String, price: Double) {
        symbolAndPrice[symbol] = price
    }
    
    func currentPrice(symbol: String) -> Double {
        return symbolAndPrice[symbol]!
    }
}

class PortfolioTests: XCTestCase {
    
    private var exchange: FixedStockExchangeStub!
    private var porfolio: Porfolio!
    
    override func setUp() {
        exchange = FixedStockExchangeStub()
        exchange.fix(symbol: "MSFT", price: 100)
        porfolio = Porfolio(exchange: exchange)
    }
    
    func test_given_5_microsoft_total_should_be_500() {
        
        porfolio.add(quantity: 5, symbol: "MSFT")
        XCTAssertEqual(500, porfolio.value)
        
    }
    
}

//PortfolioTests.defaultTestSuite.run()
```
