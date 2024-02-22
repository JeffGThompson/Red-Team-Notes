# Burp Suite: Intruder

**Room Link:** [https://tryhackme.com/room/burpsuiteintruder](https://tryhackme.com/room/burpsuiteintruder)



## What is Intruder

Intruder is Burp Suite's built-in fuzzing tool that allows for automated request modification and repetitive testing with variations in input values. By using a captured request (often from the Proxy module), Intruder can send multiple requests with slightly altered values based on user-defined configurations. It serves various purposes, such as brute-forcing login forms by substituting username and password fields with values from a wordlist or performing fuzzing attacks using wordlists to test subdirectories, endpoints, or virtual hosts. Intruder's functionality is comparable to command-line tools like Wfuzz or ffuf.

However, it's important to note that while Intruder can be used with Burp Community Edition, it is rate-limited, significantly reducing its speed compared to Burp Professional. This limitation often leads security practitioners to rely on other tools for fuzzing and brute-forcing. Nonetheless, Intruder remains a valuable tool and is worth learning how to use it effectively.

Let's explore the Intruder interface:

![Showing the intruder interface](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/2b2a10c651bee6531f8dbeb5e32733e8.png)

The initial view of Intruder presents a simple interface where we can select our target. This field will already be populated if a request has been sent from the Proxy (using `Ctrl + I` or right-clicking and selecting "Send to Intruder").

There are four sub-tabs within Intruder:

* **Positions**: This tab allows us to select an attack type (which we will cover in a future task) and configure where we want to insert our payloads in the request template.
* **Payloads**: Here we can select values to insert into the positions defined in the Positions tab. We have various payload options, such as loading items from a wordlist. The way these payloads are inserted into the template depends on the attack type chosen in the Positions tab. The Payloads tab also enables us to modify Intruder's behavior regarding payloads, such as defining pre-processing rules for each payload (e.g., adding a prefix or suffix, performing match and replace, or skipping payloads based on a defined regex).
* **Resource Pool**: This tab is not particularly useful in the Burp Community Edition. It allows for resource allocation among various automated tasks in Burp Professional. Without access to these automated tasks, this tab is of limited importance.
* **Settings**: This tab allows us to configure attack behavior. It primarily deals with how Burp handles results and the attack itself. For instance, we can flag requests containing specific text or define Burp's response to redirect (3xx) responses.

Note: The term "fuzzing" refers to the process of testing functionality or existence by applying a set of data to a parameter. For example, fuzzing for endpoints in a web application involves taking each word in a wordlist and appending it to a request URL (e.g., http://MACHINE\_IP/WORD\_GOES\_HERE) to observe the server's response.

## Positions

When using Burp Suite Intruder to perform an attack, the first step is to examine the positions within the request where we want to insert our payloads. These positions inform Intruder about the locations where our payloads will be introduced (as we will explore in upcoming tasks).

Let's navigate to the Positions tab:

![Showing the positions tab](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/1372bbafab835e10806ee6beb6681f36.png)

Notice that Burp Suite automatically attempts to identify the most probable positions where payloads can be inserted. These positions are highlighted in green and enclosed by section marks (`§`).

On the right-hand side of the interface, we find the following buttons: `Add §`, `Clear §`, and `Auto §`:

* The `Add §` button allows us to define new positions manually by highlighting them within the request editor and then clicking the button.
* The `Clear §` button removes all defined positions, providing a blank canvas where we can define our own positions.
* The `Auto §` button automatically attempts to identify the most likely positions based on the request. This feature is helpful if we previously cleared the default positions and want them back.

The following GIF demonstrates the process of adding, clearing, and automatically reselecting positions:

![Process of adding, clearing, and automatically reselecting positions](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/504d75f7c90e1c985dae5e05038f50ac.gif)

Take some time to familiarize yourself with adding, clearing, and auto-selecting positions using the Burp Suite Intruder interface.

## Payloads

In the **Payloads** tab of Burp Suite Intruder, we can create, assign, and configure payloads for our attack. This sub-tab is divided into four sections:

![Payloads sub-tab divided into four sections](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/f049dff9e1172b76c0f81ad4771b012d.png)

1. **Payload Sets**:
   * This section allows us to choose the position for which we want to configure a payload set and select the type of payload we want to use.
   * When using attack types that allow only a single payload set (Sniper or Battering Ram), the "Payload Set" dropdown will have only one option, regardless of the number of defined positions.
   * If we use attack types that require multiple payload sets (Pitchfork or Cluster Bomb), there will be one item in the dropdown for each position.
   * Note: When assigning numbers in the "Payload Set" dropdown for multiple positions, follow a top-to-bottom, left-to-right order. For example, with two positions (`username=§pentester§&password=§Expl01ted§`), the first item in the payload set dropdown would refer to the username field, and the second item would refer to the password field.
2. **Payload settings**:

* This section provides options specific to the selected payload type for the current payload set.
* For example, when using the "Simple list" payload type, we can manually add or remove payloads to/from the set using the Add text box, Paste lines, or Load payloads from a file. The Remove button removes the currently selected line, and the Clear button clears the entire list. Be cautious with loading huge lists, as it may cause Burp to crash.
*   Each payload type will have its own set of options and functionality. Explore the options available to understand the range of possibilities.\


    ![Available options](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/722a734ac5dd6437211504d3bc7bb6c2.png)



3. **Payload Processing**:

* In this section, we can define rules to be applied to each payload in the set before it is sent to the target.
* For example, we can capitalize every word, skip payloads that match a regex pattern, or apply other transformations or filtering.
* While you may not use this section frequently, it can be highly valuable when specific payload processing is required for your attack.\


4. **Payload Encoding**:

* The section allows us to customize the encoding options for our payloads.
* By default, Burp Suite applies URL encoding to ensure the safe transmission of payloads. However, there may be cases where we want to adjust the encoding behavior.
* We can override the default URL encoding options by modifying the list of characters to be encoded or unchecking the "URL-encode these characters" checkbox.

By leveraging these sections, we can create and customise payload sets to suit the specific requirements of our attacks. This level of control allows us to finely tune our payloads for effective testing and exploitation.

## Sniper

The **Sniper** attack type is the default and most commonly used attack type in Burp Suite Intruder. It is particularly effective for single-position attacks, such as password brute-force or fuzzing for API endpoints. In a Sniper attack, we provide a set of payloads, which can be a wordlist or a range of numbers, and Intruder inserts each payload into each defined position in the request.

Let's refer to our example template from before:

Example Positions

<figure><img src="../../.gitbook/assets/image (836).png" alt=""><figcaption></figcaption></figure>

In this example, we have two positions defined for the `username` and `password` body parameters. In a Sniper attack, Intruder takes each payload from the payload set and substitutes it into each defined position in turn.

Assuming we have a wordlist with three words: `burp`, `suite`, and `intruder`, Intruder would generate six requests:

| Request Number | Request Body                           |
| -------------- | -------------------------------------- |
| 1              | `username=burp&password=Expl01ted`     |
| 2              | `username=suite&password=Expl01ted`    |
| 3              | `username=intruder&password=Expl01ted` |
| 4              | `username=pentester&password=burp`     |
| 5              | `username=pentester&password=suite`    |
| 6              | `username=pentester&password=intruder` |

Observe how Intruder starts with the first position (`username`) and substitutes each payload into it, then moves to the second position (`password`) and performs the same substitution with the payloads. The total number of requests made by Intruder Sniper can be calculated as `requests = numberOfWords * numberOfPositions`.

The Sniper attack type is beneficial when we want to perform tests with single-position attacks, utilizing different payloads for each position. It allows for precise testing and analysis of different payload variations.

