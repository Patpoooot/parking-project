## Question 1

Four actors may request changes to `ParkingGarage`:

- The **pricing team** may change vehicle fees or add vehicle types.
- The **payments team** may add payment methods or change refund handling.
- The **accounting team** may change the daily report.
- The **operations team** may change how receipts are logged.

`ParkingGarage` has multiple reasons to change because it handles all four responsibilities.

## Question 2
| Principle | Class | Line(s) | Smell |
|---|---|---:|---|
| SRP | `ParkingGarage` | 12–50 | It calculates fees, processes checkouts and cancellations, formats reports, and logs receipts, giving it several reasons to change. |
| OCP | `ParkingGarage` | 13–17 | The vehicle type `switch` must be edited whenever a new vehicle type is added. |
| LSP | `CashPayment` | 11–12 | It implements `refund()` but throws `UnsupportedOperationException`, so it cannot behave as a fully substitutable `PaymentMethod`. |
| ISP | `PaymentMethod` | 3–6 | It requires every payment method to provide `refund()`, even though cash cannot refund through the machine. |
| DIP | `ParkingGarage` | 9, 30 | It creates and directly uses the concrete `FileLogger`, tying the business class to file logging. |

## Question 3
| Change request | Existing file(s) to edit |
|---|---|
| Add BUS with a fee of 15 MAD per hour | `ParkingGarage.java` — add BUS to the `fee()` switch. (`Main.java` only if you want to demonstrate it.) |
| Add a refundable mobile wallet payment method | `Main.java` to use the new method. Add a new `MobileWalletPayment.java`; the existing `PaymentMethod` interface already defines `pay()` and `refund()`. |
| Log receipts to a database instead of a file | `ParkingGarage.java` — it creates and calls `FileLogger` directly. |
| Produce the daily report in CSV format | `ParkingGarage.java` — `dailyReport()` formats the report there. |

## Question 4
`CashPayment` breaks the Liskov Substitution Principle because it implements `PaymentMethod` but cannot perform the `refund()` operation promised by that interface.

The `instanceof CashPayment` check is not an acceptable fix because it makes `ParkingGarage` depend on a specific payment class. Each new payment type with special refund behavior would require another change to `ParkingGarage`.

## Question 5
A test can check `fee()` without creating a file, but it cannot test `checkout()` without creating `parking.log`. `ParkingGarage` constructs a concrete `FileLogger` internally, and `checkout()` always calls it. There is no way to replace the logger with a test double in the current design.