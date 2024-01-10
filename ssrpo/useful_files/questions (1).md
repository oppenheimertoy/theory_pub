
# Ответы на вопросы экзамена

## 1. Определение состояния объекта, инварианта класса

Инвариант класса - это условие или набор условий, которые остаются истинными на протяжении всего жизненного цикла объекта класса. Это означает, что после того, как объект создан, его инварианты всегда остаются истинными, независимо от того, какие операции с ним выполняются. Поддержание инвариантов класса помогает обеспечить корректность и надежность работы программы.

Пример класса с контролем инварианта на С++:

```cpp
#include <stdexcept>

class BankAccount {
private:
    int balance; // Инвариант: баланс не должен быть отрицательным

public:
    BankAccount(int initialBalance) : balance(initialBalance) {
        if (initialBalance < 0) {
            throw std::invalid_argument("Initial balance cannot be negative");
        }
    }

    void deposit(int amount) {
        if (amount < 0) {
            throw std::invalid_argument("Deposit amount cannot be negative");
        }
        balance += amount;
    }

    void withdraw(int amount) {
        if (amount < 0) {
            throw std::invalid_argument("Withdrawal amount cannot be negative");
        }
        if (balance - amount < 0) {
            throw std::invalid_argument("Insufficient funds for withdrawal");
        }
        balance -= amount;
    }

    int getBalance() const {
        return balance;
    }
};
```

## 2. Определение нарушения инварианта класса

Нарушение инварианта класса происходит, когда состояние объекта выходит за рамки определенных условий инварианта. Это может привести к некорректному или неожиданному поведению объекта и программы в целом.

Пример класса с нарушением инварианта (для иллюстрации):

```cpp
class Counter {
private:
    int count; // Инвариант: count не должен быть отрицательным

public:
    Counter() : count(0) {}

    void increment() {
        count++;
    }

    void decrement() {
        count--; // Нарушение инварианта, если count становится отрицательным
    }

    int getCount() const {
        return count;
    }
};
```

## 3. Определение неизменяемого класса

Неизменяемый класс - это класс, экземпляры которого не могут быть изменены после создания. Все поля такого класса являются константами, и методы не изменяют состояние объекта.

Плюсы:
- Обеспечивает безопасность потоков, поскольку неизменяемые объекты не требуют синхронизации.
- Упрощает понимание кода и отладку.

Минусы:
- Может привести к излишнему созданию объектов.
- Ограничивает гибкость использования объектов.

Пример неизменяемого класса на С++:

```cpp
class ImmutablePoint {
private:
    const int x;
    const int y;

public:
    ImmutablePoint(int x, int y) : x(x), y(y) {}

    int getX() const {
        return x;
    }

    int getY() const {
        return y;
    }
};
```

### 1. Определение не копируемого класса

Не копируемый класс - это класс, в котором запрещено копирование объектов, то есть копирующий конструктор и оператор присваивания копированием делаются приватными или удаляются. Это используется, когда объект класса управляет ресурсом, который не должен быть скопирован или разделен (например, файловые дескрипторы или сетевые соединения).

Пример не копируемого класса на С++:

```cpp
class NonCopyable {
public:
    NonCopyable() = default;

    // Запрет копирования
    NonCopyable(const NonCopyable&) = delete;
    NonCopyable& operator=(const NonCopyable&) = delete;
};
```

### 2. Использование указателей в коде на С++ и интеллектуальные указатели

Использование "сырых" указателей в С++ может привести к ошибкам управления памятью, таким как утечки памяти или двойное освобождение ресурсов. Интеллектуальные указатели (`std::unique_ptr`, `std::shared_ptr`) автоматизируют управление памятью, уменьшая риск таких ошибок.

Пример с использованием `std::unique_ptr`:

```cpp
#include <memory>

class SomeClass {};

void useSmartPointers() {
    std::unique_ptr<SomeClass> smartPointer(new SomeClass());
    // Автоматическое управление памятью
}
```

### 3. Использование массивов в С++ и альтернативы из стандартной библиотеки

Массивы в С++ имеют фиксированный размер и не предоставляют встроенных механизмов безопасности или удобства работы с ними. Вместо них рекомендуется использовать классы стандартной библиотеки, такие как `std::vector`, `std::array` или `std::deque`, которые обеспечивают гибкость, безопасность и удобные интерфейсы.

Пример использования `std::vector`:

```cpp
#include <vector>
#include <iostream>

void useVector() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    for (int val : vec) {
        std::cout << val << ' ';
    }
}
```

### 7. Основные принципы идиомы RAII и пример на C++

RAII (Resource Acquisition Is Initialization) - идиома программирования, которая связывает управление ресурсами (например, выделение памяти, открытие файлов) с жизненным циклом объекта. При создании объекта ресурс захватывается, а при уничтожении объекта - освобождается. Это обеспечивает автоматическое и безопасное управление ресурсами даже в случае исключений.

Пример на C++:

```cpp
#include <iostream>

class RAIIExample {
private:
    int* ptr;

public:
    RAIIExample(int value) : ptr(new int(value)) {}

    ~RAIIExample() {
        delete ptr;
    }

    int getValue() const { return *ptr; }
};

void useRAII() {
    RAIIExample example(5);
    std::cout << example.getValue() << std::endl;
    // Память автоматически освобождается при уничтожении объекта example
}
```

### 8. Абстрактный класс в С++ и его отличие от интерфейса

Абстрактный класс - это класс, который содержит хотя бы один чисто виртуальный метод. Он не может быть инстанциирован напрямую. Абстрактный класс отличается от интерфейса наличием реализации некоторых методов и/или состояния.

Пример абстрактного класса и производного класса:

```cpp
class AbstractShape {
public:
    virtual void draw() const = 0; // Чисто виртуальный метод
    virtual ~AbstractShape() {}
};

class Circle : public AbstractShape {
public:
    void draw() const override {
        std::cout << "Drawing Circle" << std::endl;
    }
};
```

### 9. Класс-интерфейс в С++ и его отличие от абстрактного класса

Класс-интерфейс - это класс, все методы которого являются чисто виртуальными, и он не содержит переменных-членов. Он отличается от абстрактного класса отсутствием реализации методов и состояний.

Пример интерфейса и производного класса:

```cpp
class IDrawable {
public:
    virtual void draw() const = 0;
    virtual ~IDrawable() {}
};

class Square : public IDrawable {
public:
    void draw() const override {
        std::cout << "Drawing Square" << std::endl;
    }
};
```
