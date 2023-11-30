
### `class Config` and `class Meta`


Let's create a custom class with `class Config` and `class Meta` sections for demonstration. We'll create a simple class named `Person` with some configuration options and metadata:

```python
class Person:
    # Class-level configuration using class Config
    class Config:
        default_age = 25
        allowed_genders = ['Male', 'Female', 'Other']

    # Class-level metadata using class Meta
    class Meta:
        description = "A simple class representing a person with name, age, and gender."

    # Constructor
    def __init__(self, name, age=None, gender=None):
        self.name = name
        self.age = age if age is not None else self.Config.default_age
        self.gender = gender if gender in self.Config.allowed_genders else None

    # Method to get a brief description
    def get_description(self):
        return f"{self.name}, {self.age} years old, {self.gender}"
```

In this example:

- The `class Config` defines some configuration options like `default_age` and `allowed_genders`. These options can be accessed using `Person.Config.default_age` and `Person.Config.allowed_genders`.

- The `class Meta` provides metadata about the class, in this case, a simple description.

- The `__init__` method is the constructor, initializing instances of the `Person` class.

- The `get_description` method is a simple method that returns a brief description of a person.

You can create instances of this class and explore its properties and methods:

```python
# Creating an instance of Person
john = Person(name="John", age=30, gender="Male")

# Accessing properties
print(f"Name: {john.name}")
print(f"Age: {john.age}")
print(f"Gender: {john.gender}")

# Using the get_description method
print(john.get_description())
```

Feel free to customize the class according to your specific requirements and use cases. The `class Config` and `class Meta` sections are conventions and can be adapted based on the specific needs of your class or the frameworks/libraries you are working with.


### `@property` and `@classmethod`

Let's enhance the `Person` class by adding `@property` and `@classmethod` decorators to demonstrate the use of properties and class methods:

```python
class Person:
    # Class-level configuration using class Config
    class Config:
        default_age = 25
        allowed_genders = ['Male', 'Female', 'Other']

    # Class-level metadata using class Meta
    class Meta:
        description = "A simple class representing a person with name, age, and gender."

    # Constructor
    def __init__(self, name, age=None, gender=None):
        self.name = name
        self.age = age if age is not None else self.Config.default_age
        self.gender = gender if gender in self.Config.allowed_genders else None

    # @property to create a read-only property
    @property
    def is_adult(self):
        return self.age >= 18

    # @classmethod to create a class method
    @classmethod
    def create_person(cls, name, birth_year, gender=None):
        current_year = 2023  # Assume the current year is 2023
        age = current_year - birth_year
        return cls(name=name, age=age, gender=gender)

    # Method to get a brief description
    def get_description(self):
        return f"{self.name}, {self.age} years old, {self.gender}"

# Creating an instance of Person
john = Person(name="John", age=30, gender="Male")

# Accessing properties and methods
print(f"Name: {john.name}")
print(f"Age: {john.age}")
print(f"Gender: {john.gender}")
print(f"Is Adult: {john.is_adult}")

# Using the create_person class method
alice = Person.create_person(name="Alice", birth_year=2000)
print(alice.get_description())
```

In this updated example:

- The `@property` decorator is used to create a read-only property `is_adult`, which returns `True` if the person is 18 years or older.

- The `@classmethod` decorator is used to create a class method `create_person`, which allows creating a `Person` instance based on a birth year.

Now you can use the `is_adult` property and the `create_person` class method with instances of the `Person` class. Adjust the code based on your specific needs and requirements.






Python offers various techniques to define and write classes. The examples below demonstrate different approaches to achieve similar functionality as the previous Person class. Each example showcases a different aspect or style of class definition in Python.


### 1. Basic Class without Decorators:
```python
class Person:
    def __init__(self, name, age=None, gender=None):
        self.name = name
        self.age = age if age is not None else 25
        self.gender = gender if gender in ['Male', 'Female', 'Other'] else None

    def is_adult(self):
        return self.age >= 18

    def create_person(cls, name, birth_year, gender=None):
        current_year = 2023
        age = current_year - birth_year
        return cls(name=name, age=age, gender=gender)

    def get_description(self):
        return f"{self.name}, {self.age} years old, {self.gender}"

# Using the class
john = Person(name="John", age=30, gender="Male")
print(f"Name: {john.name}")
print(f"Is Adult: {john.is_adult()}")
```

### 2. Using a `staticmethod`:

```python
class Person:
    def __init__(self, name, age=None, gender=None):
        self.name = name
        self.age = age if age is not None else 25
        self.gender = gender if gender in ['Male', 'Female', 'Other'] else None

    @staticmethod
    def is_adult(age):
        return age >= 18

    @staticmethod
    def create_person(name, birth_year, gender=None):
        current_year = 2023
        age = current_year - birth_year
        return Person(name=name, age=age, gender=gender)

    def get_description(self):
        return f"{self.name}, {self.age} years old, {self.gender}"

# Using the class
john = Person(name="John", age=30, gender="Male")
print(f"Name: {john.name}")
print(f"Is Adult: {Person.is_adult(john.age)}")
```

### 3. Using `property` and `@classmethod`:

```python
class Person:
    default_age = 25
    allowed_genders = ['Male', 'Female', 'Other']

    def __init__(self, name, age=None, gender=None):
        self.name = name
        self.age = age if age is not None else self.default_age
        self.gender = gender if gender in self.allowed_genders else None

    @property
    def is_adult(self):
        return self.age >= 18

    @classmethod
    def create_person(cls, name, birth_year, gender=None):
        current_year = 2023
        age = current_year - birth_year
        return cls(name=name, age=age, gender=gender)

    def get_description(self):
        return f"{self.name}, {self.age} years old, {self.gender}"

# Using the class
john = Person(name="John", age=30, gender="Male")
print(f"Name: {john.name}")
print(f"Is Adult: {john.is_adult}")
```

Let's explore a few more techniques for writing Python classes. In these examples, I'll introduce class inheritance, class methods, and class variables.

### 4. Using Class Inheritance:

```python
class Person:
    def __init__(self, name, age=None, gender=None):
        self.name = name
        self.age = age if age is not None else 25
        self.gender = gender if gender in ['Male', 'Female', 'Other'] else None

    def is_adult(self):
        return self.age >= 18

    def create_person(cls, name, birth_year, gender=None):
        current_year = 2023
        age = current_year - birth_year
        return cls(name=name, age=age, gender=gender)

    def get_description(self):
        return f"{self.name}, {self.age} years old, {self.gender}"

class Employee(Person):
    def __init__(self, name, age=None, gender=None, role=None):
        super().__init__(name, age, gender)
        self.role = role

    def get_description(self):
        return f"{super().get_description()}, {self.role}"

# Using the classes
john = Person(name="John", age=30, gender="Male")
print(f"Name: {john.name}")
print(f"Is Adult: {john.is_adult()}")

alice = Employee(name="Alice", age=25, gender="Female", role="Software Engineer")
print(alice.get_description())
```

### 5. Using Class Variables:

```python
class Person:
    default_age = 25
    allowed_genders = ['Male', 'Female', 'Other']

    def __init__(self, name, age=None, gender=None):
        self.name = name
        self.age = age if age is not None else self.default_age
        self.gender = gender if gender in self.allowed_genders else None

    @property
    def is_adult(self):
        return self.age >= 18

    @classmethod
    def create_person(cls, name, birth_year, gender=None):
        current_year = 2023
        age = current_year - birth_year
        return cls(name=name, age=age, gender=gender)

    def get_description(self):
        return f"{self.name}, {self.age} years old, {self.gender}"

class Employee(Person):
    default_role = "Employee"

    def __init__(self, name, age=None, gender=None, role=None):
        super().__init__(name, age, gender)
        self.role = role if role is not None else self.default_role

    def get_description(self):
        return f"{super().get_description()}, {self.role}"

# Using the classes
john = Person(name="John", age=30, gender="Male")
print(f"Name: {john.name}")
print(f"Is Adult: {john.is_adult}")

alice = Employee(name="Alice", age=25, gender="Female")
print(alice.get_description())
```

Let's explore a few more techniques for writing Python classes, including context managers and abstract base classes.

### 6. Using a Context Manager:

```python
class FileManager:
    def __init__(self, filename, mode='r'):
        self.filename = filename
        self.mode = mode

    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_value, traceback):
        if self.file:
            self.file.close()

# Using the class as a context manager
with FileManager("example.txt", mode="w") as file:
    file.write("Hello, this is a sample file.")
```

In this example, `FileManager` is a context manager that opens a file when entering the context (`with` statement) and closes it when exiting the context.

### 7. Using Abstract Base Classes (ABC):

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius * self.radius

class Rectangle(Shape):
    def __init__(self, length, width):
        self.length = length
        self.width = width

    def area(self):
        return self.length * self.width

# Using the classes
circle = Circle(radius=5)
rectangle = Rectangle(length=4, width=6)

print(f"Circle Area: {circle.area()}")
print(f"Rectangle Area: {rectangle.area()}")
```

In this example, `Shape` is an abstract base class with an abstract method `area()`. The `Circle` and `Rectangle` classes inherit from `Shape` and provide concrete implementations of the `area` method.

### 8. Using Data Classes:

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

# Using the data class
point = Point(x=3.0, y=4.0)
print(f"Point: ({point.x}, {point.y})")
```

In this example, the `Point` class is created as a data class using the `@dataclass` decorator. Data classes automatically generate special methods such as `__init__`, `__repr__`, and `__eq__` based on the class attributes.

Let's explore a few more techniques for writing Python classes, including class decorators, mixin classes, and descriptors.

### 9. Using Class Decorators:

```python
def add_custom_method(cls):
    def custom_method(self):
        return f"Custom method called for {self.name}"

    cls.custom_method = custom_method
    return cls

@add_custom_method
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Using the class
john = Person(name="John", age=30)
print(john.custom_method())
```

In this example, the `add_custom_method` function is a class decorator that adds a custom method to the `Person` class.

### 10. Using Mixin Classes:

```python
class PrintableMixin:
    def print_info(self):
        print(f"Name: {self.name}, Age: {self.age}")

class Person(PrintableMixin):
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Using the class
john = Person(name="John", age=30)
john.print_info()
```

Here, `PrintableMixin` is a mixin class that provides additional functionality (a `print_info` method) to be mixed into the `Person` class.

### 11. Using Descriptors:

```python
class PositiveNumber:
    def __get__(self, instance, owner):
        return instance._value

    def __set__(self, instance, value):
        if value < 0:
            raise ValueError("Value must be positive.")
        instance._value = value

class Item:
    price = PositiveNumber()

    def __init__(self, name, price):
        self.name = name
        self.price = price

# Using the classes
item = Item(name="Widget", price=25)
print(f"Item: {item.name}, Price: {item.price}")
```

In this example, `PositiveNumber` is a descriptor class. The `price` attribute of the `Item` class is an instance of this descriptor, ensuring that the assigned value is always positive.


Let's explore a few more advanced techniques for writing Python classes, including metaclasses, multiple inheritance, and custom class creation.

### 12. Using Metaclasses:

```python
class MetaPerson(type):
    def __new__(cls, name, bases, attrs):
        attrs['custom_attribute'] = 100
        return super().__new__(cls, name, bases, attrs)

class Person(metaclass=MetaPerson):
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Using the class
john = Person(name="John", age=30)
print(f"Name: {john.name}, Age: {john.age}, Custom Attribute: {john.custom_attribute}")
```

In this example, `MetaPerson` is a metaclass that adds a custom attribute to any class using it as a metaclass. The `Person` class specifies `metaclass=MetaPerson` to use this metaclass.

### 13. Using Multiple Inheritance:

```python
class PrintableMixin:
    def print_info(self):
        print(f"Name: {self.name}, Age: {self.age}")

class LoggableMixin:
    def log_info(self):
        print(f"Logged: Name={self.name}, Age={self.age}")

class Person(PrintableMixin, LoggableMixin):
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Using the class
john = Person(name="John", age=30)
john.print_info()
john.log_info()
```

Here, `Person` inherits from both `PrintableMixin` and `LoggableMixin`, combining functionalities from both mixins.

### 14. Creating Custom Classes Dynamically:

```python
def create_custom_class(attribute_name):
    return type('CustomClass', (object,), {attribute_name: 42})

# Creating a custom class dynamically
CustomClass = create_custom_class('custom_attribute')
instance = CustomClass()

print(f"Custom Attribute: {instance.custom_attribute}")
```

In this example, the `create_custom_class` function dynamically creates a class named `CustomClass` with a custom attribute based on the provided `attribute_name`.


Let's explore a couple more advanced techniques for writing Python classes, including context managers as classes and dynamic attribute creation using `__getattr__`.

### 15. Creating a Context Manager as a Class:

```python
class MyContextManager:
    def __enter__(self):
        print("Entering the context")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("Exiting the context")
        if exc_type is not None:
            print(f"An exception of type {exc_type} occurred with message: {exc_value}")

# Using the context manager
with MyContextManager() as cm:
    print("Inside the context")
    # Simulating an exception
    # Uncomment the next line to see how __exit__ handles exceptions
    # raise ValueError("Something went wrong")
```

In this example, `MyContextManager` is a class-based context manager. The `__enter__` method is called when entering the context, and the `__exit__` method is called when exiting the context.

### 16. Dynamic Attribute Creation using `__getattr__`:

```python
class DynamicAttributes:
    def __init__(self, initial_data):
        self.data = initial_data

    def __getattr__(self, name):
        # This method is called when an attribute is not found
        # Dynamically create and return the attribute if it exists in the data dictionary
        if name in self.data:
            return self.data[name]
        else:
            raise AttributeError(f"'{self.__class__.__name__}' object has no attribute '{name}'")

# Using the class
obj = DynamicAttributes(initial_data={'a': 42, 'b': 'hello'})
print(obj.a)  # Accessing existing attribute
# Uncomment the next line to see how __getattr__ handles accessing a non-existing attribute
# print(obj.c)
```

In this example, `DynamicAttributes` uses the `__getattr__` method to dynamically create attributes based on the data dictionary passed during initialization.



Certainly! Let's explore a few more advanced techniques for writing Python classes, including custom metaclasses, class decorators with arguments, and abstract base classes with mixins.

### 17. Custom Metaclasses with Class Validation:

```python
class ValidateAttributesMeta(type):
    def __new__(cls, name, bases, attrs):
        for attr_name, attr_value in attrs.items():
            if not isinstance(attr_value, (int, str)):
                raise TypeError(f"Attribute '{attr_name}' must be of type int or str.")
        return super().__new__(cls, name, bases, attrs)

class Person(metaclass=ValidateAttributesMeta):
    name = "John"
    age = 30

# Uncomment the next lines to see how the metaclass validation works
# class InvalidPerson(metaclass=ValidateAttributesMeta):
#     name = "Alice"
#     age = [25, 26]
```

In this example, `ValidateAttributesMeta` is a custom metaclass that validates that all attributes in a class are either of type `int` or `str`. Applying this metaclass to the `Person` class enforces this validation.

### 18. Class Decorators with Arguments:

```python
def add_attribute(attribute_name, attribute_value):
    def decorator(cls):
        setattr(cls, attribute_name, attribute_value)
        return cls
    return decorator

@add_attribute("custom_attribute", 42)
class MyClass:
    pass

# Using the class
obj = MyClass()
print(obj.custom_attribute)
```

Here, the `add_attribute` function returns a class decorator that takes arguments (`attribute_name` and `attribute_value`) to dynamically add attributes to a class.

### 19. Abstract Base Classes with Mixins:

```python
from abc import ABC, abstractmethod

class PrintableMixin:
    @abstractmethod
    def print_info(self):
        pass

class LoggableMixin:
    @abstractmethod
    def log_info(self):
        pass

class Person(PrintableMixin, LoggableMixin):
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def print_info(self):
        print(f"Name: {self.name}, Age: {self.age}")

    def log_info(self):
        print(f"Logged: Name={self.name}, Age={self.age}")

# Using the class
john = Person(name="John", age=30)
john.print_info()
john.log_info()
```

In this example, `PrintableMixin` and `LoggableMixin` are abstract base classes with abstract methods. The `Person` class inherits from both mixins, providing concrete implementations for the abstract methods.


Let's explore a few more advanced techniques for writing Python classes, including class attributes manipulation, dynamic class creation, and using a descriptor for attribute validation.

### 20. Class Attributes Manipulation:

```python
class AttributeManipulator:
    def __init__(self, initial_data):
        for attr_name, attr_value in initial_data.items():
            setattr(self, attr_name, attr_value)

    def get_attributes(self):
        return {attr: getattr(self, attr) for attr in dir(self) if not callable(getattr(self, attr)) and not attr.startswith("__")}

# Using the class
data = {'a': 42, 'b': 'hello'}
obj = AttributeManipulator(data)
print(obj.get_attributes())
```

In this example, `AttributeManipulator` initializes its attributes dynamically based on the provided data, and the `get_attributes` method retrieves all non-callable attributes.

### 21. Dynamic Class Creation with `type`:

```python
def create_dynamic_class(class_name, attributes):
    return type(class_name, (object,), attributes)

# Creating a dynamic class
DynamicClass = create_dynamic_class('DynamicClass', {'a': 42, 'b': 'hello'})
obj = DynamicClass()

print(obj.a)
print(obj.b)
```

Here, the `create_dynamic_class` function dynamically creates a class named `DynamicClass` with attributes based on the provided dictionary.

### 22. Using a Descriptor for Attribute Validation:

```python
class PositiveNumber:
    def __set_name__(self, owner, name):
        self.name = name

    def __get__(self, instance, owner):
        return getattr(instance, self.name)

    def __set__(self, instance, value):
        if value < 0:
            raise ValueError(f"{self.name} must be a positive number.")
        setattr(instance, self.name, value)

class Item:
    price = PositiveNumber()

    def __init__(self, name, price):
        self.name = name
        self.price = price

# Using the class
item = Item(name="Widget", price=25)
print(f"Item: {item.name}, Price: {item.price}")

# Uncomment the next line to see how the descriptor handles setting a negative price
# item.price = -10
```

In this example, the `PositiveNumber` descriptor is used to ensure that the `price` attribute of the `Item` class is always a positive number.



Let's delve into more advanced techniques, including class composition, dynamic method creation, and using metaclasses for customization.

### 23. Class Composition with Mixins:

```python
class DatabaseMixin:
    def save_to_database(self):
        print(f"Saving {self} to the database")

class JSONSerializableMixin:
    def to_json(self):
        print(f"Converting {self} to JSON")

class Person(DatabaseMixin, JSONSerializableMixin):
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Using the class
john = Person(name="John", age=30)
john.save_to_database()
john.to_json()
```

In this example, `Person` utilizes class composition with mixins (`DatabaseMixin` and `JSONSerializableMixin`) to incorporate functionalities related to database interaction and JSON serialization.

### 24. Dynamic Method Creation:

```python
def dynamic_method_factory(method_name, value):
    def dynamic_method(self):
        return f"{method_name} method called with value: {value}"

    return dynamic_method

class DynamicMethods:
    pass

# Dynamically adding methods to the class
DynamicMethods.dynamic_method_1 = dynamic_method_factory("Method1", 42)
DynamicMethods.dynamic_method_2 = dynamic_method_factory("Method2", 'hello')

# Using the class
obj = DynamicMethods()
print(obj.dynamic_method_1())
print(obj.dynamic_method_2())
```

Here, the `dynamic_method_factory` function creates dynamic methods with specific behavior based on the provided `method_name` and `value`.

### 25. Metaclasses for Customization:

```python
class CustomMetaclass(type):
    def __new__(cls, name, bases, attrs):
        # Add a custom prefix to all attribute names
        prefixed_attrs = {f"custom_{attr}": value for attr, value in attrs.items()}
        return super().__new__(cls, name, bases, prefixed_attrs)

class CustomClass(metaclass=CustomMetaclass):
    value = 42

# Using the class
obj = CustomClass()
print(obj.custom_value)
```

In this example, `CustomMetaclass` is a metaclass that adds a custom prefix ("custom_") to all attribute names in the class.


Let's continue exploring more advanced techniques for writing Python classes, including class-level caching, using `__slots__` for memory optimization, and implementing a custom context manager.

### 26. Class-Level Caching:

```python
class CachedResults:
    _cache = {}

    def calculate_result(self, x):
        if x not in self._cache:
            result = x ** 2
            self._cache[x] = result
            print(f"Calculating result for {x} and caching it.")
        else:
            print(f"Using cached result for {x}.")
        return self._cache[x]

# Using the class
calculator = CachedResults()
print(calculator.calculate_result(5))  # Calculating result for 5 and caching it.
print(calculator.calculate_result(5))  # Using cached result for 5.
```

Here, `CachedResults` demonstrates class-level caching to store and reuse calculated results.

### 27. Memory Optimization with `__slots__`:

```python
class Point:
    __slots__ = ('x', 'y')

    def __init__(self, x, y):
        self.x = x
        self.y = y

# Using the class
point = Point(x=3, y=5)
print(point.x, point.y)
```

The `__slots__` attribute in the `Point` class restricts the allowed attributes, resulting in memory optimization.

### 28. Custom Context Manager Class:

```python
class MyContextManager:
    def __enter__(self):
        print("Entering the context")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("Exiting the context")
        if exc_type is not None:
            print(f"An exception of type {exc_type} occurred with message: {exc_value}")

    def perform_action(self):
        print("Performing some action in the context")

# Using the context manager
with MyContextManager() as cm:
    cm.perform_action()
```

`MyContextManager` is a class-based context manager with additional methods to perform actions within the context.

### 29. Class Attribute Validation with Descriptors:

```python
class PositiveNumber:
    def __set_name__(self, owner, name):
        self.name = name

    def __get__(self, instance, owner):
        return getattr(instance, self.name)

    def __set__(self, instance, value):
        if not isinstance(value, (int, float)) or value < 0:
            raise ValueError(f"{self.name} must be a positive number.")
        setattr(instance, self.name, value)

class Item:
    price = PositiveNumber()

    def __init__(self, name, price):
        self.name = name
        self.price = price

# Using the class
item = Item(name="Widget", price=25)
print(f"Item: {item.name}, Price: {item.price}")

# Uncomment the next line to see how the descriptor handles setting a negative price
# item.price = -10
```

`PositiveNumber` is a descriptor ensuring that the `price` attribute of the `Item` class is a positive number.


Let's explore more advanced techniques for writing Python classes, including custom iterators, dynamic class composition, and using decorators for method chaining.

### 30. Custom Iterator with `__iter__` and `__next__`:

```python
class Countdown:
    def __init__(self, start):
        self.start = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.start <= 0:
            raise StopIteration
        else:
            self.start -= 1
            return self.start + 1

# Using the class as an iterator
countdown = Countdown(5)
for num in countdown:
    print(num)
```

`Countdown` is a class that serves as a custom iterator, counting down from a specified starting value.

### 31. Dynamic Class Composition with `type`:

```python
def create_class_with_method(method_name, method_impl):
    return type('DynamicClass', (object,), {method_name: method_impl})

# Creating a dynamic class with a method
def dynamic_method(self):
    return "Dynamic method called."

DynamicClass = create_class_with_method('dynamic_method', dynamic_method)
obj = DynamicClass()

print(obj.dynamic_method())
```

In this example, the `create_class_with_method` function dynamically creates a class with a specified method.

### 32. Method Chaining with Decorators:

```python
def chainable_method(method):
    def wrapper(self, *args, **kwargs):
        result = method(self, *args, **kwargs)
        return self if result is None else result

    return wrapper

class ChainableClass:
    def __init__(self, value):
        self.value = value

    @chainable_method
    def add(self, x):
        self.value += x

    @chainable_method
    def multiply(self, y):
        self.value *= y

# Using the class with method chaining
result = ChainableClass(5).add(3).multiply(2).value
print(result)
```

The `chainable_method` decorator enables method chaining in the `ChainableClass` by modifying each method to return the instance.

### 33. Using Class Decorators for Method Modification:

```python
def capitalize_methods(cls):
    for attr_name, attr_value in cls.__dict__.items():
        if callable(attr_value) and not attr_name.startswith("__"):
            setattr(cls, attr_name, lambda self, method=attr_value: method().capitalize())
    return cls

@capitalize_methods
class TextProcessor:
    def get_text(self):
        return "original text"

# Using the class with modified methods
processor = TextProcessor()
result = processor.get_text()
print(result)
```

Here, the `capitalize_methods` class decorator modifies all callable methods in `TextProcessor` to return their original result capitalized.


Let's explore a few more advanced techniques for writing Python classes, including metaclasses for method interception, class-based decorators, and dynamic class extension.

### 34. Metaclass for Method Interception:

```python
class MethodInterceptorMeta(type):
    def __new__(cls, name, bases, attrs):
        for attr_name, attr_value in attrs.items():
            if callable(attr_value) and not attr_name.startswith("__"):
                attrs[attr_name] = cls.intercept_method(attr_name, attr_value)
        return super().__new__(cls, name, bases, attrs)

    @staticmethod
    def intercept_method(method_name, method):
        def wrapper(self, *args, **kwargs):
            print(f"Intercepting method: {method_name}")
            return method(self, *args, **kwargs)

        return wrapper

# Using the metaclass for method interception
class MyClass(metaclass=MethodInterceptorMeta):
    def my_method(self):
        print("Executing my_method")

# Creating an instance and calling the method
obj = MyClass()
obj.my_method()
```

Here, `MethodInterceptorMeta` is a metaclass that intercepts all callable methods in a class and adds custom behavior.

### 35. Class-Based Decorators:

```python
class ClassDecorator:
    def __init__(self, cls):
        self.cls = cls

    def __call__(self, *args, **kwargs):
        instance = self.cls(*args, **kwargs)
        print(f"Decorating instance of {self.cls.__name__}")
        return instance

# Applying the class-based decorator
@ClassDecorator
class DecoratedClass:
    def __init__(self, value):
        self.value = value

# Creating an instance of the decorated class
decorated_instance = DecoratedClass(value=42)
print(decorated_instance.value)
```

`ClassDecorator` is a class-based decorator that wraps the instantiation of a class, allowing for additional setup or modification.

### 36. Dynamic Class Extension:

```python
def extend_class(cls, extension):
    class ExtendedClass(cls, extension):
        pass
    return ExtendedClass

# Creating a class and an extension
class BaseClass:
    def base_method(self):
        print("Executing base_method")

class Extension:
    def extension_method(self):
        print("Executing extension_method")

# Creating a dynamically extended class
ExtendedClass = extend_class(BaseClass, Extension)

# Using the dynamically extended class
obj = ExtendedClass()
obj.base_method()
obj.extension_method()
```

Here, `extend_class` is a function that dynamically creates a new class by inheriting from two existing classes.


Let's explore a few more advanced techniques for writing Python classes, including class-based mixins, dynamic method addition, and using class-level attributes for configuration.

### 37. Class-Based Mixins:

```python
class JSONSerializableMixin:
    def to_json(self):
        import json
        return json.dumps(self.__dict__)

class PrintableMixin:
    def print_info(self):
        print(f"Information: {self.__dict__}")

class Person(JSONSerializableMixin, PrintableMixin):
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Using the class with mixins
john = Person(name="John", age=30)
print(john.to_json())
john.print_info()
```

In this example, `JSONSerializableMixin` and `PrintableMixin` are class-based mixins providing additional functionality to the `Person` class.

### 38. Dynamic Method Addition:

```python
class DynamicMethodAddition:
    def add_method(self, method_name, method_impl):
        setattr(self, method_name, method_impl)

# Using the class with dynamically added methods
obj = DynamicMethodAddition()
obj.add_method("custom_method", lambda self: "Custom method called.")
print(obj.custom_method())
```

Here, `DynamicMethodAddition` allows dynamically adding methods to an instance of the class.

### 39. Class-Level Attributes for Configuration:

```python
class ConfigurableClass:
    _config = {
        'debug': False,
        'max_attempts': 3
    }

    def __init__(self, name):
        self.name = name

    @classmethod
    def configure(cls, config):
        cls._config.update(config)

    def perform_action(self):
        if self._config['debug']:
            print(f"Debug mode: Performing action for {self.name}")
        else:
            print(f"Performing action for {self.name}")

# Using the class with class-level configuration
obj = ConfigurableClass(name="Object1")
obj.perform_action()

# Configuring the class
ConfigurableClass.configure({'debug': True})
obj.configure({'max_attempts': 5})
obj.perform_action()
```

In this example, `ConfigurableClass` has class-level attributes for configuration, allowing dynamic adjustment of behavior.


Let's delve into more advanced techniques for writing Python classes, including dynamic attribute creation, multiple inheritance with mixins, and class-based context managers.

### 40. Dynamic Attribute Creation with `__getattr__`:

```python
class DynamicAttributes:
    def __init__(self, initial_data):
        self.data = initial_data

    def __getattr__(self, name):
        # This method is called when an attribute is not found
        # Dynamically create and return the attribute if it exists in the data dictionary
        if name in self.data:
            return self.data[name]
        else:
            raise AttributeError(f"'{self.__class__.__name__}' object has no attribute '{name}'")

# Using the class
obj = DynamicAttributes(initial_data={'a': 42, 'b': 'hello'})
print(obj.a)  # Accessing existing attribute
# Uncomment the next line to see how __getattr__ handles accessing a non-existing attribute
# print(obj.c)
```

In this example, `DynamicAttributes` uses the `__getattr__` method to dynamically create attributes based on the data dictionary passed during initialization.

### 41. Multiple Inheritance with Mixins:

```python
class PrintableMixin:
    def print_info(self):
        print(f"Name: {self.name}, Age: {self.age}")

class LoggableMixin:
    def log_info(self):
        print(f"Logged: Name={self.name}, Age={self.age}")

class Person(PrintableMixin, LoggableMixin):
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Using the class
john = Person(name="John", age=30)
john.print_info()
john.log_info()
```

Here, `PrintableMixin` and `LoggableMixin` are mixins providing additional functionalities, and `Person` inherits from both mixins.

### 42. Class-Based Context Managers:

```python
class MyContextManager:
    def __enter__(self):
        print("Entering the context")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("Exiting the context")
        if exc_type is not None:
            print(f"An exception of type {exc_type} occurred with message: {exc_value}")

    def perform_action(self):
        print("Performing some action in the context")

# Using the class as a context manager
with MyContextManager() as cm:
    cm.perform_action()
```

`MyContextManager` is a class-based context manager with additional methods to perform actions within the context.

### 43. Advanced Class-Based Decorator:

```python
class ClassDecorator:
    def __init__(self, param):
        self.param = param

    def __call__(self, cls):
        class DecoratedClass(cls):
            def __init__(self, *args, **kwargs):
                super().__init__(*args, **kwargs)
                self.param_value = self.param

        return DecoratedClass

# Applying the class-based decorator
@ClassDecorator(param="DecoratorParam")
class DecoratedClass:
    def __init__(self, value):
        self.value = value

# Creating an instance of the decorated class
decorated_instance = DecoratedClass(value=42)
print(decorated_instance.value)
print(decorated_instance.param_value)
```

Here, `ClassDecorator` is a class-based decorator that adds an additional parameter to the decorated class.


Let's continue exploring more advanced techniques for writing Python classes, including metaclasses for validation, class-level caching, and using descriptors for attribute management.

### 44. Metaclass for Attribute Validation:

```python
class AttributeValidationMeta(type):
    def __new__(cls, name, bases, attrs):
        for attr_name, attr_value in attrs.items():
            if isinstance(attr_value, int) and attr_value < 0:
                raise ValueError(f"Attribute '{attr_name}' must be a non-negative integer.")
        return super().__new__(cls, name, bases, attrs)

# Using the metaclass for attribute validation
class PositiveAttributesClass(metaclass=AttributeValidationMeta):
    positive_value = 42

# Uncomment the next line to see how the metaclass validation works
# class NegativeAttributesClass(metaclass=AttributeValidationMeta):
#     negative_value = -10
```

`AttributeValidationMeta` is a metaclass that validates integer attributes in a class to ensure they are non-negative.

### 45. Class-Level Caching with `functools.lru_cache`:

```python
from functools import lru_cache

class CachedResults:
    @lru_cache(maxsize=None)
    def calculate_result(self, x):
        print(f"Calculating result for {x}")
        return x ** 2

# Using the class with caching
calculator = CachedResults()
print(calculator.calculate_result(5))  # Calculating result for 5
print(calculator.calculate_result(5))  # Using cached result for 5
```

Here, `CachedResults` utilizes `functools.lru_cache` to cache method results based on input parameters.

### 46. Descriptor for Attribute Management:

```python
class PositiveNumber:
    def __set_name__(self, owner, name):
        self.name = name

    def __get__(self, instance, owner):
        return getattr(instance, self.name)

    def __set__(self, instance, value):
        if not isinstance(value, (int, float)) or value < 0:
            raise ValueError(f"{self.name} must be a positive number.")
        setattr(instance, self.name, value)

class Item:
    price = PositiveNumber()

    def __init__(self, name, price):
        self.name = name
        self.price = price

# Using the class with a descriptor for attribute management
item = Item(name="Widget", price=25)
print(f"Item: {item.name}, Price: {item.price}")

# Uncomment the next line to see how the descriptor handles setting a negative price
# item.price = -10
```

`PositiveNumber` is a descriptor ensuring that the `price` attribute of the `Item` class is always a positive number.



Let's dive into more advanced techniques for writing Python classes, covering topics like abstract base classes, custom metaclasses, and class-based decorators.

### 47. Abstract Base Classes (ABC):

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius**2

# Using the abstract base class and its subclass
# Uncomment the next line to see how attempting to instantiate Shape raises an error
# shape = Shape()
circle = Circle(radius=5)
print(circle.area())
```

`Shape` is an abstract base class (ABC) with an abstract method `area()`. The `Circle` class is a concrete implementation of the `Shape` ABC.

### 48. Custom Metaclass for Singleton Pattern:

```python
class SingletonMeta(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            instance = super().__call__(*args, **kwargs)
            cls._instances[cls] = instance
        return cls._instances[cls]

# Using the metaclass for a singleton class
class SingletonClass(metaclass=SingletonMeta):
    def __init__(self, value):
        self.value = value

# Creating instances of the singleton class
singleton1 = SingletonClass(value=1)
singleton2 = SingletonClass(value=2)
print(singleton1.value, singleton2.value)  # Both instances share the same state
```

`SingletonMeta` is a custom metaclass implementing the Singleton pattern, ensuring only one instance of a class is created.

### 49. Class-Based Decorator with Arguments:

```python
class ClassDecoratorWithArgs:
    def __init__(self, arg):
        self.arg = arg

    def __call__(self, cls):
        class DecoratedClass(cls):
            def __init__(self, *args, **kwargs):
                super().__init__(*args, **kwargs)
                self.decorator_arg = self.arg

        return DecoratedClass

# Applying the class-based decorator with arguments
@ClassDecoratorWithArgs(arg="DecoratorArg")
class DecoratedClass:
    def __init__(self, value):
        self.value = value

# Creating an instance of the decorated class
decorated_instance = DecoratedClass(value=42)
print(decorated_instance.value)
print(decorated_instance.decorator_arg)
```

`ClassDecoratorWithArgs` is a class-based decorator that accepts arguments and adds an additional attribute to the decorated class.

### 50. Dynamic Class Extension with Decorators:

```python
def extend_class_with_decorator(extension):
    def decorator(cls):
        class ExtendedClass(cls, extension):
            pass
        return ExtendedClass
    return decorator

# Creating a class and an extension
class BaseClass:
    def base_method(self):
        print("Executing base_method")

class Extension:
    def extension_method(self):
        print("Executing extension_method")

# Creating a dynamically extended class using a decorator
@extend_class_with_decorator(extension=Extension)
class ExtendedClass:
    pass

# Using the dynamically extended class
obj = ExtendedClass()
obj.base_method()
obj.extension_method()
```

Here, `extend_class_with_decorator` is a function that dynamically creates a new class by inheriting from two existing classes using a decorator.

