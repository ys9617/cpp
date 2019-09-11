# SOLID Design Principles

## Single Responsibility Principle (SRP)

SRP states that every module, class, or function should have 
responsibility over a single part of the functionality provided 
by the software, and that responsibility should be entirely 
encapsulated by the class.

```cpp
class Journal {
private :
    string title;
    vector<string> entries;

public :
    explicit Journal(const string& title) : title(title) {}
};

```

```cpp
void Joural::add(const string& entry) {
    static int count = 1;
    entries.push_back(boost::lexical_cast<string>(count++) + 
        ": " + entry);
}
```
add function is suitable for SRP.


```cpp
void Journal::save(const string& filename) {
    ofstream ofs(filename);
    for (auto& s : entries) {
        ofs << s << endl;
    }
}
```
save functionality should be handled seperatly from the Jouranal 
class because save function is not suitable for SRP.

Ex) Journal::save -> Persistancemanager class
```cpp
class Journal {
    ...

    friend class PersistanceManeger;
};

class PersistanceManager {
public :
    static void save(const Journal& j, const string& filename) {
        ofstream ofs(filename);
        for (auto& s : j.entris) {
            ofs << s << endl;
        }
    }
};
```


## Open-Closed Principle (OCP)

OCP states that software entities (classes, modules, functions...) 
should be open for extension, but closed for modification.

```cpp
enum class Color { Red, Green, Blue };
enum class Size { Small, Medium, Large };

class Product {
private :
    string name;
    Color color;
    Size size;

public :
    ...     // some functions
};
```

Ex) adding various filtering functionalies
```cpp
class ProductFilter {
private :
    using Items = vector<Product*>;

public :
    Items by_color(Items items, Color color);
    Items by_size(Items items, Size size);
};

ProductFilter::Items ProductFilter::by_color(Items items, Color color) {
    Items result;
    for (auto& i : items) {
        if (i->color == color) {
            result.push_back(i);
        }
    }

    return result;
};

ProductFilter::Items ProductFilter::by_size(Items items, Size size) {
    Items result;
    for (auto& i : items) {
        if (i->size == size) {
            result.push_back(i);
        }
    }

    return result;
};
```

```cpp
template<typename T>
class Specification {
public :
    virtual bool is_satisfied(T* item) = 0;

    AndSpecification<T> operator&&(Specification&& other) {
        return AndSpecification<T>(*this, other);
    }
};

template<typename T>
class AndSpecification : Specification<T> {
private :
    Specification<T>& first, second;

public :
    AndSpecification(Specification<T>& first, Specification<T>& second)
    : first(first), second(second) {}

    bool is_satisfied(T* items) override {
        return first.is_satisfied(item) && second.is_satisfied(item);
    }
};

template<typename T>
class Filter {
public :
    virtual vector<T*> filter(vector<T*> items, Specification<T>& spec) = 0;
};

class ColorSpecification : Specification<Product> {
    Color color;

    explicit ColorSpecification(const Color color) : color(color) {}

    bool is_satisfied(Product* item) override {
        return item->color == color;
    }
};

class BetterFilter : filter<Product> {
public :
    vector<Product*> filter(vector<Product*> items, Specification<Product>& spec) override {
        vector<Product*> result;
        for (auto& i : items) {
            if (spec.is_satisfied(i)) {
                result.push_back(i);
            }
        }

        return result;
    }
}

Product apple = {"Apple", Color::Green, Size::Small };
Product tree = {"Tree", Color::Green, Size::large };
Product house = {"House", Color::Blue, Size::Large };
vector<Product*> all = { &apple, &tree, &house };

auto green_and_large = ColorSpecification(Color::green) && SizeSpecification(Size::large);
auto green_large_things = BetterFilter::filter(all, green_and_large);

```





