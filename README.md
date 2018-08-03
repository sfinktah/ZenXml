# zen::Xml: Overview
![Logo](zenXml.png)
zen::Xml 2.4.1
   

Simple C++  XML Processing

var searchBox = new SearchBox("searchBox", "search",false,'Search');

- [Main Page](index.html)
- [Namespaces](namespaces.html)
- [Classes](annotated.html)

Overview 

- [Rationale](http://zenxml.sourceforge.net/index.html#sec_Rationale)
- [Quick Start](http://zenxml.sourceforge.net/index.html#sec_Quick_Start)
- [Supported Platforms](http://zenxml.sourceforge.net/index.html#sec_Supported_Platforms)
- [Flexible Programming Model](http://zenxml.sourceforge.net/index.html#sec_Flexible_Programming_Model)
- [Structured XML element access](http://zenxml.sourceforge.net/index.html#sec_Structured_XML_element_access)
- [Access XML attributes](http://zenxml.sourceforge.net/index.html#sec_Access_XML_attributes)
- [Automatic conversion for built-in arithmetic types](http://zenxml.sourceforge.net/index.html#sec_Automatic_conversion_built_in)
- [Automatic conversion for string-like types](http://zenxml.sourceforge.net/index.html#sec_Automatic_conversion_string)
- [Automatic conversion for STL container types](http://zenxml.sourceforge.net/index.html#sec_Automatic_conversion_STL)
- [Support for user-defined types](http://zenxml.sourceforge.net/index.html#sec_Support_user_defined)
- [Structured user types](http://zenxml.sourceforge.net/index.html#sec_Structured_user_types)
- [Type Safety](http://zenxml.sourceforge.net/index.html#sec_Type_Safety)

# 
Rationale

zen::Xml is an XML library serializing structured user data in a convenient way. Using compile-time information gathered by techniques of template metaprogramming it minimizes the manual overhead required and frees the user from implementing fundamental type conversions by himself. Basic data types such as

- all built-in arithmetic numbers,
- all kinds of string classes and "string-like" types,
- all types defined as STL containers

are handled automatically. Thereby a large number of recurring problems is solved by the library:

- generic number to string conversions
- generic char to wchar_t conversions (UTF) for custom string classes in a platform independent manner
- serialization of arbitrary STL container types
- simple integration: header-only, no extra dependencies, fully portable
- support arbitrary string classes everywhere: for file names, XML element names, attribute names, values, ...
- XML library built on C++14 with focus on elegance, minimal code size, flexibility and performance
- easily extensible API: allow for internationalization, fine-granular error handling, and custom file I/O

The design follows the philosophy of the Loki library: 
[http://loki-lib.sourceforge.net/index.php?n=Main.Philosophy](http://zenxml.sourceforge.net/http://loki-lib.sourceforge.net/index.php?n=Main.Philosophy)

# 
Quick Start

1. Download zen::Xml

2. Setup one of the following preprocessor macros for your project to identify the platform (this is only required if you use C-stream-based file IO) 

    
    ZEN_WIN
    
    ZEN_LINUX   
    ZEN_MAC
    

3. For optimal performance define this global macro in release build: (following convention of the `assert` macro) 

    
    NDEBUG
    

4. Include the main header: 

    
    #include <zenxml/xml.h>
    

5. Start serializing user data:

    
    size_t a = 10;
    double b = 2.0;
    int    c = -1;
    

    
    [zen::XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc; //empty XML document
    [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc); //the simplest way to fill the document is to use a data output proxy
    out["elem1"](a); //
    out["elem2"](b); //map data types to XML elements
    out["elem3"](c); //try
    {
        [save](http://zenxml.sourceforge.net/namespacezen.html#adeeb6b2318097382ae47aa939fc15d4d)(doc, "file.xml"); //throw zen::XmlFileError
    }
    catch (const[zen::XmlFileError](http://zenxml.sourceforge.net/structzen_1_1_xml_file_error.html)& e) { /* handle error */ }
    

The following XML file will be created: 

    <?xml version="1.0" encoding="UTF-8"?>
    <Root>
        <elem1>10</elem1>
        <elem2>2.000000</elem2>
        <elem3>-1</elem3>
    </Root>
    

Load an XML file and map its content to user data: 

    
    [zen::XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc; //empty XML document
    try
    {
        doc = [load](http://zenxml.sourceforge.net/namespacezen.html#a872a48c0616e7f12ae8caca464835e00)("file.xml"); //throw XmlFileError, XmlParsingError
    }
    catch (const[zen::XmlError](http://zenxml.sourceforge.net/structzen_1_1_xml_error.html)& e) { /* handle error */ }
    
    [zen::XmlIn](http://zenxml.sourceforge.net/classzen_1_1_xml_in.html) in(doc); //the simplest way to read the document is to use a data input proxy
    in["elem1"](a); //
    in["elem2"](b); //map XML elements into user data
    in["elem3"](c); ////check for mapping errors, i.e. missing elements or conversion errors: you may consider these as warnings onlyif (in.errorsOccured())
    {
       std::vector<std::wstring> failedElements = in.getErrorsAs<std::wstring>();
       /* generate error message showing the XML element names that failed to convert */
    }
    

# 
Supported Platforms

zen::Xml is written in a platform independent manner and runs on any C++14-compliant compiler, e.g. Microsoft Visual C++, MinGW (Windows) and GCC, Clang (Linux and macOS).

Note: In order to enable C++14 features in GCC it is required to specify either of the following compiler options: 

    -std=c++14
    -std=gnu++14
    

# 
Flexible Programming Model

Depending on what granularity of control is required in a particular application, zen::Xml allows the user to choose between full control or simplicity. 

The library is structured into the following parts, each of which can be used in isolation: 

<File>

|

| [io.h](http://zenxml.sourceforge.net/io_8h_source.html)

|
<Byte Stream>

|

| [parser.h](http://zenxml.sourceforge.net/parser_8h_source.html)

|
<Document Object Model>

|

| [bind.h](http://zenxml.sourceforge.net/bind_8h_source.html)

|
<C++ user data>

- Save an XML document to memory 
    
    [zen::XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc;
    
        ... //fill it
    std::string stream = [serialize](http://zenxml.sourceforge.net/namespacezen.html#afaa4920e275078e6c8009fbdf58b57ee)(doc); //throw ()/* you now have a binary XML stream */[saveStream](http://zenxml.sourceforge.net/namespacezen.html#a4ba7bbaa14a787b07fc13da9145aabe2)(stream, "file.xml"); //throw XmlFileError//if all you need is to store XmlDoc in a file direcly you can use zen::save() instead

- Load XML document from memory 
    
    //get XML byte stream:
    
    std::string stream = [loadStream](http://zenxml.sourceforge.net/namespacezen.html#a04fe23c3bd9b7d03309620b5ea763607)("file.xml"); //throw XmlFileError[zen::XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc;
    //parse byte stream into an XML document:[parse](http://zenxml.sourceforge.net/namespacezen.html#a1ae1a4688d724b554fe3bf4638700477)(stream, doc); //throw XmlParsingError//if all you need is to load an XmlDoc from a file you can use zen::load() directly

- Fine-granular error checking with the data input proxy 
    
    [zen::XmlIn](http://zenxml.sourceforge.net/classzen_1_1_xml_in.html) in(doc);
    //map XML elements into user dataif (!in["elem1"](a))
       throw MyCustomException();
    if (!in["elem2"](b))
       throw MyCustomException();
    if (!in["elem3"](c))
       throw MyCustomException();
    
    //if (in.errorsOccured()) ...  <- not required here: contains the same conversion errors checked manually before

- Access the Document Object Model directly (without input/output proxy) 

The full power of type conversions which is available via the input/output proxy classes [zen::XmlIn](http://zenxml.sourceforge.net/classzen_1_1_xml_in.html) and [zen::XmlOut](classzen_1_1_xml_out.html) is also available for the document object model! 
    
    using namespace [zen](namespacezen.html);
    [XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc;
    
    [XmlElement](http://zenxml.sourceforge.net/classzen_1_1_xml_element.html)& child = doc.[root](classzen_1_1_xml_doc.html#ad4a9594d93885fc1a12db28e8246648d)().[addChild](classzen_1_1_xml_element.html#a653caffa6fad89db7d14f67f987ad0f9)("elem1");
    child.[setValue](http://zenxml.sourceforge.net/classzen_1_1_xml_element.html#aaf3a26f6199fc88cce7d9d911ba21b01)(1234);
    
    [save](http://zenxml.sourceforge.net/namespacezen.html#adeeb6b2318097382ae47aa939fc15d4d)(doc, "file.xml"); //throw XmlFileError

    
    using namespace [zen](http://zenxml.sourceforge.net/namespacezen.html);
    [XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc = [load](namespacezen.html#a872a48c0616e7f12ae8caca464835e00)("file.xml"); //throw XmlFileError, XmlParsingError[XmlElement](http://zenxml.sourceforge.net/classzen_1_1_xml_element.html)* child = doc.[root](classzen_1_1_xml_doc.html#ad4a9594d93885fc1a12db28e8246648d)().[getChild](classzen_1_1_xml_element.html#a3ab82b1720460487f4afabcd115d0c7e)("elem1");
    if (child)
    {
        int value = -1;
        if (!child->[getValue](http://zenxml.sourceforge.net/classzen_1_1_xml_element.html#a5ac9d586a5668c2c64e3c06c6203b070)(value))
            ... //handle conversion error
    }
    else
        ... //XML element not found

# 
Structured XML element access

    
    //write a value into one deeply nested XML element - note the different types used seamlessly: char[], wchar_t[], char, wchar_t, int
    [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc);
    out["elemento1"][L"элемент2"][L"要素3"][L"στοιχείο4"]["elem5"][L"元素6"][L'元']['z'](-1234);
    

The resulting XML: 

    <?xml version="1.0" encoding="UTF-8"?>
    <Root>
        <elemento1>
            <элемент2>
                <要素3>
                    <στοιχείο4>
                        <elem5>
                            <元素6>
                                <元>
                                    <z>-1234</z>
                                </元>
                            </元素6>
                        </elem5>
                    </στοιχείο4>
                </要素3>
            </элемент2>
        </elemento1>
    </Root>
    

# 
Access XML attributes

    
    [zen::XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc;
    [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc);
    out["elem"].attribute("attr1",   -1); //
    out["elem"].attribute("attr2",  2.1); //write data into XML attributes
    out["elem"].attribute("attr3", true); //[save](http://zenxml.sourceforge.net/namespacezen.html#adeeb6b2318097382ae47aa939fc15d4d)(doc, "file.xml"); //throw XmlFileError

The resulting XML: 

    <?xml version="1.0" encoding="UTF-8"?>
    <Root>
        <elem attr1="-1" attr2="2.1" attr3="true"/>
    </Root>
    

# 
Automatic conversion for built-in arithmetic types

All built-in arithmetic types and `bool` are detected at compile time and a proper conversion is applied. Common conversions for integer-like types such as `int`, `long`, `long long`, ect. as well as floating point types are optimized for maximum performance.

    
    [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc);
    
    
    out["int"]   (-1234);
    out["double"](1.23);
    out["float"] (4.56f);
    out["ulong"] (1234UL);
    out["bool"]  (false);
    

The resulting XML: 

    <?xml version="1.0" encoding="UTF-8"?>
    <Root>
        <int>-1234</int>
        <double>1.23</double>
        <float>4.56</float>
        <ulong>1234</ulong>
        <bool>false</bool>
    </Root>
    

# 
Automatic conversion for string-like types

The document object model of zen::Xml internally stores all names and values as a std::string. Consequently everything that is not a std::string but is "string-like" is UTF-converted into a std::string representation. By default zen::Xml accepts all character arrays like `char[]`, `wchar_t[]`, `char*`, `wchar_t*`, single characters like `char`, `wchar_t`, standard string classes like `std::string`, `std::wstring` and user-defined string classes. If the input string is based on `char`, it will simply be copied and thereby preserves any local encodings. If the input string is based on `wchar_t` it will be converted to an UTF-8 encoded `std::string`. The correct `wchar_t` encoding of the system will be detected at compile time, for example UTF-16 on Windows, UTF-32 on most Linux distributions.

Note: User-defined string classes are automatically supported if they fulfill the following string concept by defining:

1. A typedef named `value_type` for the underlying character type: must be `char` or `wchar_t`
2. A member function `c_str()` returning something that can be converted into a `const value_type*`
3. A member function `length()` returning the number of characters returned by `c_str()`

    
    std::string  elem1 = "elemento1";
    
    std::wstring elem2 = L"элемент2";
    wxString     elem3 = L"要素3";
    MyString     elem4 = L"στοιχείο4";
    
    [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc);
    
    out["string"]    (elem1);
    out["wstring"]   (elem2);
    out["wxString"]  (elem3);
    out["MyString"]  (elem4);
    out["char[6]"]   ("elem5");
    out["wchar_t[4]"](L"元素6");
    out["wchar_t"]   (L'元');
    out["char"]      ('z');
    

The resulting XML: 

    <?xml version="1.0" encoding="UTF-8"?>
    <Root>
        <string>elemento1</string>
        <wstring>элемент2</wstring>
        <wxString>要素3</wxString>
        <MyString>στοιχείο4</MyString>
        <char[6]>elem5</char[6]>
        <wchar_t[4]>元素6</wchar_t[4]>
        <wchar_t>元</wchar_t>
        <char>z</char>
    </Root>
    

# 
Automatic conversion for STL container types

- User-defined STL compatible types are automatically supported if they fulfill the following container concept by defining:
1. A typedef named `value_type` for the underlying element type of the container
2. A typedef named `iterator` for a non-const iterator into the container
3. A typedef named `const_iterator` for a const iterator into the container 

4. A member function `begin()` returning an iterator pointing to the first element in the container
5. A member function `end()` returning an iterator pointing just after the last element in the container
6. A member function `insert()` with the signature `iterator insert(iterator position, const value_type& x)`
7. A member function `clear()` removing all elements from the container

- In order to support combinations of user types and STL containers such as `std::vector<MyType>` or `std::vector<std::list<MyType>>` it is sufficient to only integrate `MyType` into zen::Xml. 

See [Support for user-defined types](http://zenxml.sourceforge.net/index.html#sec_Support_user_defined)

    
    std::deque   <float>         testDeque;
    
    std::list    <size_t>        testList;
    std::map     <double, char>  testMap;
    std::multimap<short, double> testMultiMap;
    std::set     <int>           testSet;
    std::multiset<std::string>   testMultiSet;
    std::vector  <wchar_t>       testVector;
    std::vector  <std::list<wchar_t>> testVectorList;
    std::pair    <char, wchar_t> testPair;
    
    /* fill container */[zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc);
    
    out["deque"]    (testDeque);
    out["list"]     (testList);
    out["map"]      (testMap);
    out["multimap"] (testMultiMap);
    out["set"]      (testSet);
    out["multiset"] (testMultiSet);
    out["vector"]   (testVector);
    out["vect_list"](testVectorList);
    out["pair" ]    (testPair);
    

The resulting XML: 

    <?xml version="1.0" encoding="UTF-8"?>
    <Root>
        <deque>
            <Item>1.234</Item>
            <Item>5.678</Item>
        </deque>
        <list>
            <Item>1</Item>
            <Item>2</Item>
        </list>
        <map>
            <Item>
                <one>1.1</one>
                <two>a</two>
            </Item>
            <Item>
                <one>2.2</one>
                <two>b</two>
            </Item>
        </map>
        <multimap>
            <Item>
                <one>3</one>
                <two>99</two>
            </Item>
            <Item>
                <one>3</one>
                <two>100</two>
            </Item>
            <Item>
                <one>4</one>
                <two>101</two>
            </Item>
        </multimap>
        <set>
            <Item>1</Item>
            <Item>2</Item>
        </set>
        <multiset>
            <Item>1</Item>
            <Item>1</Item>
            <Item>2</Item>
        </multiset>
        <vector>
            <Item>Ä</Item>
            <Item>Ö</Item>
        </vector>
        <vect_list>
            <Item>
                <Item>ä</Item>
                <Item>ö</Item>
                <Item>ü</Item>
            </Item>
            <Item>
                <Item>ä</Item>
                <Item>ö</Item>
                <Item>ü</Item>
            </Item>
        </vect_list>
        <pair>
            <one>a</one>
            <two>â</two>
        </pair>
    </Root>
    

# 
Support for user-defined types

User types can be integrated into zen::Xml by providing specializations of [zen::readText()](http://zenxml.sourceforge.net/namespacezen.html#acaf85ab94b61882f957afcd355386bff) and [zen::writeText()](namespacezen.html#a2ce2998296871fc2f4718ceceb22a23f) or [zen::readStruc()](namespacezen.html#a2bdcecfe7435ef11cedbce47d4e72ee1) and [zen::writeStruc()](namespacezen.html#a29ddb823fe0a195f19a64448881b8bf6). The first pair should be used for all non-structured types that can be represented as a simple text string. This specialization is then used to convert the type to XML elements and XML attributes. The second pair should be specialized for structured types that require an XML representation as a hierarchy of elements. This specialization is used when converting the type to XML elements only. 

See section [Type Safety](http://zenxml.sourceforge.net/index.html#sec_Type_Safety) for a discussion of type categories. 

Example: Specialization for an enum type

    
    enum UnitTime
    
    {
        UNIT_SECOND,
        UNIT_MINUTE,
        UNIT_HOUR
    };
    
    
    template <> inlinevoid[zen::writeText](http://zenxml.sourceforge.net/namespacezen.html#a2ce2998296871fc2f4718ceceb22a23f)(const UnitTime& value, std::string& output)
    {
        switch (value)
        {
            case UNIT_SECOND: output = "second"; break;
            case UNIT_MINUTE: output = "minute"; break;
            case UNIT_HOUR:   output = "hour"  ; break;
        }
    }
    
    template <> inlinebool[zen::readText](http://zenxml.sourceforge.net/namespacezen.html#acaf85ab94b61882f957afcd355386bff)(const std::string& input, UnitTime& value)
    {
        std::string tmp = input;
        zen::trim(tmp);
        if (tmp == "second")
            value = UNIT_SECOND;
        elseif (tmp == "minute")
            value = UNIT_MINUTE;
        elseif (tmp == "hour")
            value = UNIT_HOUR;
        elsereturnfalse;
        returntrue;
    }
    

Example: Brute-force specialization for an enum type

    
    template <> inline
    void[zen::writeText](http://zenxml.sourceforge.net/namespacezen.html#a2ce2998296871fc2f4718ceceb22a23f)(const EnumType& value, std::string& output)
    {
        output = zen::numberTo<std::string>(static_cast<int>(value)); //treat enum like an integer
    }
    
    template <> inlinebool[zen::readText](http://zenxml.sourceforge.net/namespacezen.html#acaf85ab94b61882f957afcd355386bff)(const std::string& input, EnumType& value)
    {
        value = static_cast<EnumType>(zen::stringTo<int>(input)); //treat enum like an integerreturntrue;
    }
    

Example: Specialization for a structured user type

    
    struct Config
    
    {
        int a;
        std::wstring b;
    };
    
    
    template <> inlinevoid[zen::writeStruc](http://zenxml.sourceforge.net/namespacezen.html#a29ddb823fe0a195f19a64448881b8bf6)(const Config& value, XmlElement& output)
    {
        XmlOut out(output);
        out["number" ](value.a);
        out["address"](value.b);
    }
    
    template <> inlinebool[zen::readStruc](http://zenxml.sourceforge.net/namespacezen.html#a2bdcecfe7435ef11cedbce47d4e72ee1)(const XmlElement& input, Config& value)
    {
        XmlIn in(input);
        bool rv1 = in["number" ](value.a);
        bool rv2 = in["address"](value.b);
        return rv1 && rv2;
    }
    
    int main()
    {
        Config cfg = { 2,  L"Abc 3" };
    
        std::vector<Config> cfgList;
        cfgList.push_back(cfg);
    
        [zen::XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc;
        [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc); //write to Xml via output proxy
        out["config"](cfgList);
        [save](http://zenxml.sourceforge.net/namespacezen.html#adeeb6b2318097382ae47aa939fc15d4d)(doc, "file.xml"); //throw XmlFileError
    }
    

The resulting XML: 

    <?xml version="1.0" encoding="UTF-8"?>
    <Root>
        <config>
            <Item>
                <number>2</number>
                <address>Abc 3</address>
            </Item>
        </config>
    </Root>
    

# 
Structured user types

Although it is possible to enable conversion of structured user types by specializing [zen::readStruc()](http://zenxml.sourceforge.net/namespacezen.html#a2bdcecfe7435ef11cedbce47d4e72ee1) and [zen::writeStruc()](namespacezen.html#a29ddb823fe0a195f19a64448881b8bf6) (see [Support for user-defined types](index.html#sec_Support_user_defined)), this approach has one drawback: If a mapping error occurs when converting an XML element to structured user data, for example a child-element is missing, the input proxy class [zen::XmlIn](classzen_1_1_xml_in.html) is only able to detect that the whole conversion failed. It cannot say which child-elements in particular failed to convert. 

Therefore it may be appropriate to convert structured types by calling subroutines in order to enable fine-granular logging:

    
    void readConfig(const[zen::XmlIn](http://zenxml.sourceforge.net/classzen_1_1_xml_in.html)& in, Config& cfg)
    
    {
        in["number" ](value.a); //failed conversions will now be logged for each single item by XmlIn
        in["address"](value.b); //instead of only once for the complete Config type!
    }
    
    
    void loadConfig(const wxString& filename, Config& cfg)
    {
        [zen::XmlDoc](http://zenxml.sourceforge.net/classzen_1_1_xml_doc.html) doc; //empty XML documenttry
        {
            [load](http://zenxml.sourceforge.net/namespacezen.html#a872a48c0616e7f12ae8caca464835e00)(filename, doc); //throw XmlFileError, XmlParsingError
        }
        catch (const[zen::XmlError](http://zenxml.sourceforge.net/structzen_1_1_xml_error.html)& e) { /* handle error */ }
    
        [zen::XmlIn](http://zenxml.sourceforge.net/classzen_1_1_xml_in.html) in(doc); 
     
        [zen::XmlIn](http://zenxml.sourceforge.net/classzen_1_1_xml_in.html) inConfig = in["config"]; //get input proxy for child element "config"
      
        readConfig(inConfig, cfg); //map child element to user data by calling subroutine//check for mapping errors: errors occuring in subroutines are considered, too!if (in.errorsOccured())
           /* show mapping errors */
    }
    

# 
Type Safety

zen::Xml heavily uses methods of compile-time introspection in order to free the user from managing basic type conversions by himself. Thereby it is important to find the right balance between automatic conversions and type safety so that program correctness is not compromised. In the context of XML processing three fundamental type categories can be recognized:

- string-like types: `std::string, wchar_t*, char[], wchar_t, wxString, MyStringClass, ...`
- to-string-convertible types: any string-like type, all built-in arithmetic numbers, `bool`
- structured types: any to-string-convertible type, STL containers, `std::pair`, structured user types

These categories can be seen as a sequence of inclusive sets: 

    -----------------------------
    | structured                |  Used as: XML element value
    | ------------------------- |           Conversion via: readStruc(), writeStruc() - may be specialized for user-defined types!
    | | to-string-convertible | |  Used as: XML element/attribute value
    | | ---------------       | |           Conversion via: readText(), writeText() - may be specialized for user-defined types!
    | | | string-like |       | |  Used as: XML element/attribute value or element name
    | | ---------------       | |           Conversion via: utfCvrtTo<>()
    | ------------------------- |
    -----------------------------
    

A practical implication of this design is that conversions that do not make sense in a particular context simply lead to compile-time errors: 

    
    [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc);
    
    out[L'Z'](someValue); //fine: a wchar_t is acceptable as an element name
    out[1234](someValue); //compiler error: an integer is NOT "string-like"!

    
    int i = 0;
    
    std::vector<int> v;
    
    [zen::XmlOut](http://zenxml.sourceforge.net/classzen_1_1_xml_out.html) out(doc);
    out["elem1"](i); //fine: both i and v can be converted to an XML element
    out["elem2"](v); //
    
    out["elem"].attribute("attr1", i); //fine: an integer can be converted to an XML attribute
    out["elem"].attribute("attr2", v); //compiler error: a std::vector<int> is NOT "to-string-convertible"!

AuthorZenju

Email: zenju AT freefilesync DOT org 

---

Generated by  [![doxygen](http://zenxml.sourceforge.net/doxygen.png)](http://www.doxygen.org/index.html) 1.8.8
