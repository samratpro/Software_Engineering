# SOLID Principles Overview

## What are SOLID Principles?

SOLID is an acronym for five design principles intended to make software designs more understandable, flexible, and maintainable.

- **S** - Single Responsibility Principle (SRP)
- **O** - Open/Closed Principle (OCP)
- **L** - Liskov Substitution Principle (LSP)
- **I** - Interface Segregation Principle (ISP)
- **D** - Dependency Inversion Principle (DIP)

---

## S - Single Responsibility Principle (SRP)

### Explanation
A class should have only **ONE reason to change**. Each class should have a single, well-defined responsibility.

### Wrong Code ❌
```java
class Account {
    void openAccount() {
        System.out.println("openAccount");
    }
    
    void saveAccount() {
        System.out.println("saveAccount");
    }
    
    void calculateInterest() {
        System.out.println("calculateInterest");
    }
    
    void printAccountDetails() {
        System.out.println("printAccountDetails");
    }
}
```

**Problem:** The `Account` class has multiple responsibilities:
- Managing accounts (openAccount, saveAccount)
- Calculating interest (calculateInterest)
- Printing logs (printAccountDetails)

This violates SRP because the class has multiple reasons to change.

### Right Code ✅
```java
class Account {
    void openAccount() {
        System.out.println("openAccount");
    }
    
    void saveAccount() {
        System.out.println("saveAccount");
    }
}

class Interest {
    void calculateInterest() {
        System.out.println("calculateInterest");
    }
}

class Logs {
    void printAccountDetails() {
        System.out.println("printAccountDetails");
    }
}

public class AccountSolid {
    public static void main(String[] args) {
        Account a = new Account();
        a.openAccount();
        a.saveAccount();
        
        Interest i = new Interest();
        i.calculateInterest();
        
        Logs p = new Logs();
        p.printAccountDetails();
    }
}
```

**Solution:** Each class now has a single responsibility:
- `Account` - Manages account operations
- `Interest` - Handles interest calculations
- `Logs` - Handles printing/logging

---

## O - Open/Closed Principle (OCP)

### Explanation
Software entities should be **open for extension** but **closed for modification**. You should be able to add new functionality without changing existing code.

### Wrong Code ❌
```java
class LoanCalculator {
    void calculateInterest(String loanType, double amount) {
        if (loanType.equals("home")) {
            double result = 0.08 * amount;
            System.out.println("Home loan interest: " + result);
        } else if (loanType.equals("car")) {
            double result = 0.10 * amount;
            System.out.println("Car loan interest: " + result);
        } else if (loanType.equals("education")) {
            double result = 0.05 * amount;
            System.out.println("Education loan interest: " + result);
        }
        // Adding a new loan type requires modifying this class!
    }
}
```

**Problem:** 
- Adding a new loan type requires modifying the existing `LoanCalculator` class
- Violates the "closed for modification" principle
- Code becomes harder to maintain as more loan types are added

### Right Code ✅
```java
interface LoanProvide {
    void calculateInterest(double amount);
}

class Homeloan implements LoanProvide {
    double result = 0;
    
    public void calculateInterest(double amount) {
        result = 0.08 * amount;
        System.out.println("Home loan interest: " + result);
    }
}

class Carloan implements LoanProvide {
    double result = 0;
    
    public void calculateInterest(double amount) {
        result = 0.10 * amount;
        System.out.println("Car loan interest: " + result);
    }
}

class Eduloan implements LoanProvide {
    double result = 0;
    
    public void calculateInterest(double amount) {
        result = 0.05 * amount;
        System.out.println("Education loan interest: " + result);
    }
}

public class Loan {
    public static void main(String[] args) {
        Homeloan h = new Homeloan();
        h.calculateInterest(50000);
        
        Carloan c = new Carloan();
        c.calculateInterest(40000);
        
        Eduloan e = new Eduloan();
        e.calculateInterest(60000);
    }
}
```

**Solution:** 
- New loan types can be added by creating new classes that implement `LoanProvide`
- No need to modify existing code
- Easy to extend with new loan types (e.g., PersonalLoan, BusinessLoan)

---

## L - Liskov Substitution Principle (LSP)

### Explanation
Objects of a superclass should be replaceable with objects of its subclasses without breaking the application. Subclasses should extend, not replace, parent behavior.

**In simple terms:** If class B is a subtype of class A, we should be able to replace A with B without disrupting the behavior of our program.

### Wrong Code ❌
```java
interface Account {
    void accountStatement();
    void withdraw(double amount);
}

class RegularAccount implements Account {
    public void accountStatement() {
        System.out.println("Account Statement");
    }
    
    public void withdraw(double amount) {
        System.out.println("Amount: " + amount);
    }
}

class FixedDepositAccount implements Account {
    public void accountStatement() {
        System.out.println("Account Statement");
    }
    
    public void withdraw(double amount) {
        // Fixed deposits don't allow withdrawal!
        throw new UnsupportedOperationException("Cannot withdraw from FD");
    }
}
```

**Problem:**
- `FixedDepositAccount` cannot fulfill the `withdraw()` contract
- Throws an exception instead of proper implementation
- Cannot substitute `FixedDepositAccount` for `Account` without breaking functionality
- Violates LSP

### Right Code ✅
```java
interface Account {
    void accountStatement();
}

class RegularAccount implements Account {
    public void accountStatement() {
        System.out.println("Account Statement");
    }
    
    void withdraw(double amount) {
        System.out.println("Amount: " + amount);
    }
}

class FixedDepositAccount implements Account {
    public void accountStatement() {
        System.out.println("Account Statement");
    }
}

public class Bank {
    public static void main(String[] args) {
        RegularAccount r = new RegularAccount();
        r.accountStatement();
        r.withdraw(500);
        
        FixedDepositAccount f = new FixedDepositAccount();
        f.accountStatement();
    }
}
```

**Solution:**
- Only common behavior is in the `Account` interface
- `withdraw()` is specific to `RegularAccount`, not forced on all account types
- `FixedDepositAccount` only implements what it can properly support
- Both classes can substitute for `Account` interface without issues

---

## I - Interface Segregation Principle (ISP)

### Explanation
Clients should not be forced to depend on interfaces they don't use. Create specific interfaces rather than one general-purpose interface.

**In simple terms:** Many client-specific interfaces are better than one general-purpose interface.

### Wrong Code ❌
```java
interface BankOperations {
    void accountStatement();
    void withdraw(double amount);
    void deposit(double amount);
    void applyForLoan();
}

class SavingsAccount implements BankOperations {
    public void accountStatement() { 
        System.out.println("Savings Account Statement");
    }
    
    public void withdraw(double amount) { 
        System.out.println("Withdrawn: " + amount);
    }
    
    public void deposit(double amount) { 
        System.out.println("Deposited: " + amount);
    }
    
    public void applyForLoan() {
        // Savings accounts don't offer loans!
        throw new UnsupportedOperationException("Loans not available");
    }
}

class FixedDepositAccount implements BankOperations {
    public void accountStatement() { 
        System.out.println("FD Account Statement");
    }
    
    public void withdraw(double amount) {
        // FD doesn't allow withdrawal
        throw new UnsupportedOperationException("Cannot withdraw");
    }
    
    public void deposit(double amount) {
        // FD doesn't allow deposits after opening
        throw new UnsupportedOperationException("Cannot deposit");
    }
    
    public void applyForLoan() {
        throw new UnsupportedOperationException("Loans not available");
    }
}
```

**Problem:**
- Classes are forced to implement methods they don't need
- Leads to empty implementations or throwing exceptions
- Interface is too "fat" with too many methods
- Violates ISP

### Right Code ✅
```java
interface AccountOperations {
    void accountStatement();
}

interface WithdrawableAccount {
    void withdraw(double amount);
}

interface DepositableAccount {
    void deposit(double amount);
}

interface LoanAccount {
    void applyForLoan();
}

class RegularAccount implements AccountOperations, WithdrawableAccount, DepositableAccount {
    public void accountStatement() {
        System.out.println("Regular Account Statement");
    }
    
    public void withdraw(double amount) {
        System.out.println("Withdrawn: " + amount);
    }
    
    public void deposit(double amount) {
        System.out.println("Deposited: " + amount);
    }
}

class FixedDepositAccount implements AccountOperations {
    public void accountStatement() {
        System.out.println("FD Account Statement");
    }
}

class LoanEligibleAccount implements AccountOperations, LoanAccount {
    public void accountStatement() {
        System.out.println("Loan Account Statement");
    }
    
    public void applyForLoan() {
        System.out.println("Loan application submitted");
    }
}
```

**Solution:**
- Interfaces are segregated based on specific functionalities
- Classes implement only what they need
- No forced implementation of unused methods
- More flexible and maintainable design

---

## D - Dependency Inversion Principle (DIP)

### Explanation
- High-level modules should not depend on low-level modules. Both should depend on abstractions.
- Abstractions should not depend on details. Details should depend on abstractions.

**In simple terms:** Depend on interfaces/abstractions, not on concrete classes.

### Wrong Code ❌
```java
class EmailService {
    void sendEmail(String message) {
        System.out.println("Email sent: " + message);
    }
}

class NotificationService {
    private EmailService emailService = new EmailService();
    
    void notify(String message) {
        emailService.sendEmail(message); // Tightly coupled to EmailService
    }
}

public class Main {
    public static void main(String[] args) {
        NotificationService notif = new NotificationService();
        notif.notify("Hello!");
        // What if we want to send SMS instead? We'd have to modify NotificationService!
    }
}
```

**Problem:**
- `NotificationService` is tightly coupled to `EmailService`
- Cannot easily switch to SMS or other notification methods
- Hard to test (can't mock EmailService)
- Violates DIP - high-level module depends on low-level module

### Right Code ✅
```java
interface MessageService {
    void sendMessage(String message);
}

class EmailService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Email sent: " + message);
    }
}

class SMSService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("SMS sent: " + message);
    }
}

class PushNotificationService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Push notification sent: " + message);
    }
}

class NotificationService {
    private MessageService messageService;
    
    // Dependency Injection through constructor
    NotificationService(MessageService messageService) {
        this.messageService = messageService;
    }
    
    void notify(String message) {
        messageService.sendMessage(message);
    }
}

public class Main {
    public static void main(String[] args) {
        // Can easily switch between different message services
        NotificationService emailNotif = new NotificationService(new EmailService());
        emailNotif.notify("Hello via Email!");
        
        NotificationService smsNotif = new NotificationService(new SMSService());
        smsNotif.notify("Hello via SMS!");
        
        NotificationService pushNotif = new NotificationService(new PushNotificationService());
        pushNotif.notify("Hello via Push!");
    }
}
```

**Solution:**
- `NotificationService` depends on the `MessageService` abstraction, not concrete implementations
- Can easily switch between Email, SMS, Push notifications without modifying `NotificationService`
- Easy to test with mock implementations
- New message services can be added without changing existing code
- Both high-level and low-level modules depend on abstraction

---

## Summary

| Principle | Key Idea | Benefit |
|-----------|----------|---------|
| **SRP** | One class = One responsibility | Easy to maintain and understand |
| **OCP** | Open for extension, closed for modification | Add features without breaking existing code |
| **LSP** | Subtypes must be substitutable for their base types | Reliable inheritance hierarchies |
| **ISP** | Many specific interfaces > One general interface | Classes aren't forced to implement unused methods |
| **DIP** | Depend on abstractions, not concrete classes | Flexible, testable, loosely coupled code |

## Benefits of Following SOLID Principles

✅ **Maintainability** - Code is easier to update and modify  
✅ **Scalability** - Easy to add new features  
✅ **Testability** - Code is easier to test  
✅ **Flexibility** - Code adapts to change better  
✅ **Reusability** - Components can be reused  
✅ **Readability** - Code is cleaner and easier to understand  

---

**Remember:** SOLID principles are guidelines, not strict rules. Use them wisely to create better software!
