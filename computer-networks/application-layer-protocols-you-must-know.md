The **Application Layer** is the highest layer in the TCP/IP and OSI models, acting as the interface between the user application and the network services. Understanding its core protocols is non-negotiable.

This guide breaks down the essential protocols, focusing on their **purpose**, **transport protocol**, and **default port number**—the three details most frequently tested.


## 1. HTTP (HyperText Transfer Protocol)

HTTP is the backbone of the World Wide Web.

* **Purpose:** Used for accessing and transferring hypermedia documents (like HTML files) on the web.
* **Transport Protocol:** **TCP** (reliability is critical for web page transfer).
* **Default Port:** **80** (HTTP) and **443** (**HTTPS** - the secure, encrypted version).
* **Key Concept:** Uses a **client-server** model and is generally **stateless** (meaning the server does not retain any information about past client requests).

## 2. DNS (Domain Name System)

DNS is often called the "phonebook" of the internet.

* **Purpose:** Translates human-readable **domain names** (like `google.com`) into machine-readable **IP addresses** (like `142.250.67.238`).
* **Transport Protocol:** Primarily **UDP** (for fast, non-guaranteed query/response), but uses **TCP** for zone transfers (when transferring large amounts of data between DNS servers).
* **Default Port:** **53**.
* **Key Concept:** Functions as a **distributed, hierarchical database**.

## 3. Email Protocols (SMTP, POP3, IMAP)

Email relies on a trio of protocols for sending and retrieval.

| Protocol | Purpose | Transport Protocol | Port |
| :--- | :--- | :--- | :--- |
| **SMTP** (Simple Mail Transfer Protocol) | **Sending** and transferring mail *between* mail servers. | TCP | 25 |
| **POP3** (Post Office Protocol v3) | **Retrieving** mail from a server (typically **downloads and deletes** from the server). | TCP | 110 |
| **IMAP** (Internet Message Access Protocol) | **Retrieving** mail from a server (allows mail to be managed **on the server** without downloading). | TCP | 143 |

## 4. FTP (File Transfer Protocol)

FTP is designed for reliable, high-speed file transfer.

* **Purpose:** Transfers files between a client and a server.
* **Transport Protocol:** **TCP** (ensuring all file parts arrive correctly).
* **Default Ports:** **21** (for the **Control** connection, which handles commands) and **20** (for the **Data** connection, which handles the actual file transfer).
* **Key Concept:** Uses **two** separate TCP connections simultaneously.

## 5. DHCP (Dynamic Host Configuration Protocol)

DHCP simplifies network administration by automating device setup.

* **Purpose:** Dynamically assigns **IP addresses**, subnet masks, default gateway, and DNS server information to devices connecting to a network.
* **Transport Protocol:** **UDP** (used because the host doesn't have an IP address yet to establish a reliable TCP connection).
* **Default Ports:** **67** (Server) and **68** (Client).


## Quick Reference Table (The Essentials)

| Protocol | Function | Transport | Port |
| :--- | :--- | :--- | :--- |
| **HTTP** | Web Browsing | TCP | 80/443 |
| **DNS** | Name-to-IP Translation | UDP/TCP | 53 |
| **SMTP** | Sending Email | TCP | 25 |
| **POP3** | Receiving Email (Download & Delete) | TCP | 110 |
| **IMAP** | Receiving Email (Manage on Server) | TCP | 143 |
| **FTP** | File Transfer | TCP | 20/21 |
| **DHCP** | Auto IP Address Assignment | UDP | 67/68 |