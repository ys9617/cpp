# Builder

## simple builder

```cpp
class HtmlElement {
public :
    string name;
    string text;
    vector<HtmlElement> elements;
    
    HtmlElement(const string& n, const string& t)
    : name(n), text(t) {}

    string str(int indent = 0) const {
        // ...
    }
}
```

```cpp
class HtmlBuilder {
public :
    HtmlElement root;

    HtmlBuilder(string root_name) { root.name = root_name; }

    void add_child(string child_name, string child_text) {
        HtmlElement e(child_name, child_text);
        root.elements.emplace_back(e);
    }

    string str() { return root.str(); }
}

```
